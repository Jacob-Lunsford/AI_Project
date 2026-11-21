<template>
  <div class="app" :class="['theme-' + theme, { 'no-motion': !animationsEnabled }]">
    <header class="top">
      <div class="brand">
        <div class="mark"><span>✳</span></div>
        <div>
          ROSHAMBO
          <small>NEURAL ARENA / SYSTEM 4.2</small>
        </div>
      </div>
      <div class="top-actions">
        <button class="instructions-button mono" type="button" @click="settingsOpen = true">
          ⚙ &nbsp; SETTINGS
        </button>
        <button class="instructions-button mono" type="button" @click="instructionsOpen = true">
          ? &nbsp; INSTRUCTIONS
        </button>
        <div class="online"><i></i> MODEL ACTIVE</div>
      </div>
    </header>

    <section class="hero">
      <div class="eyebrow">NEURAL STRATEGY · COUNTER-PREDICTION · HUMAN INTUITION VS MACHINE LEARNING</div>
      <h1>PLAY THE <em>ADAPTIVE SYSTEM.</em></h1>
      <div class="subtitle">
        A synthetic opponent tuned to detect patterns, respond in real time, and adjust its play as your decisions evolve.
      </div>
      <div class="subtitle secondary">COUNTER-INTELLIGENCE / PATTERN MODEL / LIVE RESPONSE</div>
    </section>

    <main class="grid">
      <section class="panel arena" :class="'round-' + outcome">
        <div class="arena-head">
          <span>◉ &nbsp; LIVE MATCH</span>
          <span class="round">ROUND {{ String(round).padStart(2, '0') }}</span>
        </div>

        <div class="battle">
          <div class="fighter human">
            <div class="orb" :class="{ winner: outcome === 'win' }">
              <div class="ring"></div>
              {{ playerMove !== null ? moves[playerMove].icon : '◈' }}
            </div>
            <div class="fighter-name">YOU</div>
            <div class="fighter-tag">HUMAN PLAYER</div>
          </div>

          <div class="vs">VS</div>

          <div class="fighter ai">
            <div class="orb" :class="{ thinking, winner: outcome === 'lose' }">
              <div class="ring"></div>
              {{ thinking ? '◌' : aiMove !== null ? moves[aiMove].icon : '◈' }}
            </div>
            <div class="fighter-name">N.E.M.O. · AI</div>
            <div class="fighter-tag">{{ matchComplete ? 'MATCH COMPLETE / NEXT MATCH INCOMING' : thinking ? 'AI ANALYZING PATTERNS…' : 'ADAPTIVE MODEL / ONLINE' }}</div>
          </div>
        </div>

        <div class="result" :class="outcome">{{ resultText }}</div>

        <div class="match-progress" v-if="matchFormat !== 'endless'">
          <div class="match-progress-head">
            <span>MATCH {{ matchNumber }} / BEST OF {{ matchFormat }}</span>
            <span>FIRST TO {{ matchTarget }}</span>
          </div>
          <div class="match-progress-scores">
            <div>
              <span>YOU <b>{{ matchWins }}</b></span>
              <div class="mini-meter"><i class="player-meter" :style="{ width: (matchWins / matchTarget) * 100 + '%' }"></i></div>
            </div>
            <div>
              <span>N.E.M.O. <b>{{ matchLosses }}</b></span>
              <div class="mini-meter"><i class="ai-meter" :style="{ width: (matchLosses / matchTarget) * 100 + '%' }"></i></div>
            </div>
          </div>
        </div>
        <div v-else class="match-progress endless-progress">
          ENDLESS MODE <span>·</span> PLAY AS LONG AS YOU LIKE
        </div>

        <div class="choices">
          <button
            v-for="(move, key) in moves"
            :key="key"
            class="choice"
            :disabled="thinking || matchComplete"
            @click="play(key)"
          >
            <span class="choice-icon">{{ move.icon }}</span>
            <span class="choice-label">{{ move.label }}</span>
          </button>
        </div>

        <div class="hint">SELECT YOUR MOVE &nbsp;·&nbsp; THE ENGINE LEARNS AS YOU PLAY</div>
      </section>

      <aside class="side">
        <section class="panel score">
          <div class="card-head">
            <span>SESSION SCORE</span>
            <button class="reset mono" @click="reset">↻ RESET</button>
          </div>
          <div class="scores">
            <div class="you">
              <strong>{{ wins }}</strong>
              <small>YOU</small>
            </div>
            <div class="bot">
              <strong>{{ losses }}</strong>
              <small>N.E.M.O.</small>
            </div>
            <div>
              <strong>{{ draws }}</strong>
              <small>DRAWS</small>
            </div>
          </div>
          <div class="match-statistics">
            <div>
              <strong>{{ totalGames }}</strong>
              <small>TOTAL GAMES</small>
            </div>
            <div>
              <strong>{{ winPercentage }}%</strong>
              <small>WIN % · ALL ROUNDS</small>
            </div>
          </div>
        </section>

        <section class="panel side-panel">
          <div class="card-head">
            <span>◉ &nbsp; PATTERN ANALYSIS</span>
            <span class="confidence">{{ confidence }}%</span>
          </div>
          <div class="insight" v-html="insight"></div>
          <div class="meter"><span :style="{ width: confidence + '%' }"></span></div>
          <div class="card-head">YOUR RECENT MOVES</div>
          <div class="recent">
            <i v-if="!recentMoves.length" v-for="n in 5" :key="n">·</i>
            <i v-else v-for="(move, index) in recentMoves" :key="index" :class="{ active: index === 0 }">{{ moves[move].icon }}</i>
          </div>
        </section>

        <section class="panel side-panel">
          <div class="card-head">
            <span>▤ &nbsp; MATCH LOG</span>
            <span>{{ history.length }} ROUNDS</span>
          </div>
          <div class="feed" v-if="history.length">
            <div v-for="entry in history.slice(0, 5)" :key="entry.round" class="feed-row">
              <span>#{{ String(entry.round).padStart(2, '0') }} &nbsp; {{ moves[entry.you].icon }} {{ moves[entry.ai].icon }}</span>
              <b :class="entry.class">{{ entry.label }}</b>
            </div>
          </div>
          <div v-else class="empty">Awaiting first match…</div>
        </section>
      </aside>
    </main>

    <footer class="footer">
      <span>INTERFACE / COPY / ENGINE: SYNTHETIC SYSTEM · VUE 3</span>
      <span>THE MODEL LEARNS AS YOU PLAY.</span>
    </footer>

    <div
      v-if="instructionsOpen"
      class="instructions-overlay"
      role="dialog"
      aria-modal="true"
      aria-labelledby="instructions-title"
      @click.self="instructionsOpen = false"
      @keydown.escape="instructionsOpen = false"
    >
      <section class="instructions-modal">
        <div class="instructions-head">
          <div>
            <small>ROSHAMBO / GAME GUIDE</small>
            <h2 id="instructions-title">HOW TO PLAY</h2>
          </div>
          <button
            class="instructions-close mono"
            type="button"
            aria-label="Close instructions"
            @click="instructionsOpen = false"
          >
            ×
          </button>
        </div>

        <p>Select one of the five moves. N.E.M.O. analyzes and counters your play after a short delay. Choose a best-of match in Settings, or play in Endless mode.</p>

        <div class="rules-grid">
          <div><strong>ROCK ✊</strong><span>beats Scissors, Lizard</span></div>
          <div><strong>PAPER ✋</strong><span>beats Rock, Spock</span></div>
          <div><strong>SCISSORS ✌️</strong><span>beats Paper, Lizard</span></div>
          <div><strong>LIZARD 🦎</strong><span>beats Paper, Spock</span></div>
          <div><strong>SPOCK 🖖</strong><span>beats Rock, Scissors</span></div>
        </div>

        <div class="instructions-stats">
          <p><strong>Winning:</strong> Choose a move that beats N.E.M.O.'s move.</p>
          <p><strong>Drawing:</strong> Matching moves are draws.</p>
          <p><strong>Best of:</strong> First to a majority of wins clinches the match. Draws do not count toward the match target.</p>
          <p><strong>Total Games:</strong> Wins, losses, and draws combined.</p>
          <p><strong>Win %:</strong> Wins divided by total games. Draws count in the denominator.</p>
        </div>

        <button class="instructions-start mono" type="button" @click="instructionsOpen = false">
          START PLAYING
        </button>
      </section>
    </div>

    <div
      v-if="settingsOpen"
      class="instructions-overlay"
      role="dialog"
      aria-modal="true"
      aria-labelledby="settings-title"
      @click.self="settingsOpen = false"
      @keydown.escape="settingsOpen = false"
    >
      <section class="instructions-modal settings-modal">
        <div class="instructions-head">
          <div>
            <small>ROSHAMBO / CONFIGURATION</small>
            <h2 id="settings-title">ARENA SETTINGS</h2>
          </div>
          <button
            class="instructions-close mono"
            type="button"
            aria-label="Close settings"
            @click="settingsOpen = false"
          >
            ×
          </button>
        </div>

        <div class="setting-group">
          <label class="setting-label">ARENA COLOR SYSTEM</label>
          <div class="theme-options">
            <button
              v-for="option in themes"
              :key="option.value"
              class="theme-option"
              :class="['theme-' + option.value, { selected: theme === option.value }]"
              type="button"
              :aria-pressed="theme === option.value"
              @click="theme = option.value"
            >
              <i></i>
              <span>{{ option.label }}</span>
            </button>
          </div>
        </div>

        <div class="setting-row">
          <div>
            <label class="setting-label" for="match-format">MATCH FORMAT</label>
            <small class="setting-description">A clinched match starts the next one automatically. Session totals stay intact.</small>
          </div>
          <select
            id="match-format"
            class="setting-select mono"
            :value="matchFormat"
            :disabled="thinking || matchComplete"
            @change="changeMatchFormat($event.target.value)"
          >
            <option value="3">BEST OF 3</option>
            <option value="5">BEST OF 5</option>
            <option value="7">BEST OF 7</option>
            <option value="endless">ENDLESS</option>
          </select>
        </div>

        <div class="setting-row motion-setting">
          <div>
            <label class="setting-label" for="motion-toggle">ARENA ANIMATIONS</label>
            <small class="setting-description">Toggle the spinning rings, transitions, and result effects.</small>
          </div>
          <button
            id="motion-toggle"
            class="toggle"
            :class="{ enabled: animationsEnabled }"
            type="button"
            role="switch"
            :aria-checked="animationsEnabled"
            @click="animationsEnabled = !animationsEnabled"
          >
            <span>{{ animationsEnabled ? 'ON' : 'OFF' }}</span>
            <i></i>
          </button>
        </div>

        <p class="settings-note">Settings apply immediately. Changing match format resets only the current match score.</p>
        <button class="instructions-start mono" type="button" @click="settingsOpen = false">
          APPLY &amp; RETURN TO ARENA
        </button>
      </section>
    </div>

    <div v-if="toast" class="toast">{{ toast }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      moves: {
        rock: { label: 'ROCK', icon: '✊', beats: ['scissors', 'lizard'] },
        paper: { label: 'PAPER', icon: '✋', beats: ['rock', 'spock'] },
        scissors: { label: 'SCISSORS', icon: '✌️', beats: ['paper', 'lizard'] },
        lizard: { label: 'LIZARD', icon: '🦎', beats: ['paper', 'spock'] },
        spock: { label: 'SPOCK', icon: '🖖', beats: ['rock', 'scissors'] },
      },
      themes: [
        { value: 'cyan', label: 'CYAN CORE' },
        { value: 'violet', label: 'VIOLET NEBULA' },
        { value: 'gold', label: 'SOLAR GOLD' },
      ],
      theme: 'cyan',
      animationsEnabled: true,
      matchFormat: '3',
      matchNumber: 1,
      matchWins: 0,
      matchLosses: 0,
      matchComplete: false,
      matchTimer: null,
      playerMove: null,
      aiMove: null,
      thinking: false,
      outcome: '',
      resultText: '',
      wins: 0,
      losses: 0,
      draws: 0,
      totalGames: 0,
      round: 1,
      history: [],
      playerHistory: [],
      toast: '',
      toastTimer: null,
      instructionsOpen: false,
      settingsOpen: false,
    }
  },
  computed: {
    matchTarget() {
      return this.matchFormat === 'endless' ? null : Math.ceil(Number(this.matchFormat) / 2)
    },
    winPercentage() {
      if (this.totalGames === 0) return 0
      return Math.round((this.wins / this.totalGames) * 100)
    },
    recentMoves() {
      return this.playerHistory.slice(-5).reverse()
    },
    confidence() {
      if (this.playerHistory.length < 2) return 12
      const counts = this.playerHistory.reduce((acc, move) => {
        acc[move] = (acc[move] || 0) + 1
        return acc
      }, {})
      const max = Math.max(...Object.values(counts))
      return Math.min(94, Math.round(30 + (max / this.playerHistory.length) * 64))
    },
    insight() {
      if (this.playerHistory.length < 2) {
        return 'Collecting behavioral data. Make a few moves to activate <b>adaptive counter-prediction</b>.'
      }

      const counts = this.playerHistory.reduce((acc, move) => {
        acc[move] = (acc[move] || 0) + 1
        return acc
      }, {})
      const favorite = Object.keys(counts).sort((a, b) => counts[b] - counts[a])[0]

      if (counts[favorite] === 1) {
        return 'Your play is <b>hard to predict</b>. N.E.M.O. updates its model after every round.'
      }

      return `Pattern detected: <b>${this.moves[favorite].label}</b> appears in ${Math.round((counts[favorite] / this.playerHistory.length) * 100)}% of your moves. Counter-strategy loaded.`
    },
  },
  methods: {
    changeMatchFormat(format) {
      this.matchFormat = format
      this.matchWins = 0
      this.matchLosses = 0
      this.matchNumber = 1
      this.matchComplete = false
      if (this.matchTimer) window.clearTimeout(this.matchTimer)
      this.matchTimer = null
      this.outcome = ''
      this.resultText = ''
      this.playerMove = null
      this.aiMove = null
      this.showToast(`MATCH FORMAT UPDATED — ${format === 'endless' ? 'ENDLESS' : `BEST OF ${format}`}`)
    },
    randomMove() {
      const keys = Object.keys(this.moves)
      return keys[Math.floor(Math.random() * keys.length)]
    },
    predictPlayer() {
      if (!this.playerHistory.length) return this.randomMove()

      const counts = this.playerHistory.reduce((acc, move) => {
        acc[move] = (acc[move] || 0) + 1
        return acc
      }, {})
      const ranked = Object.keys(counts).sort((a, b) => counts[b] - counts[a])

      if (Math.random() < 0.65) return ranked[0]
      if (this.playerHistory.length >= 3 && Math.random() < 0.25) {
        return this.playerHistory[this.playerHistory.length - 1]
      }
      return this.randomMove()
    },
    showToast(message) {
      this.toast = message
      if (this.toastTimer) window.clearTimeout(this.toastTimer)
      this.toastTimer = window.setTimeout(() => {
        this.toast = ''
      }, 2400)
    },
    play(move) {
      if (this.thinking) return

      this.thinking = true
      this.playerMove = move
      this.outcome = ''
      this.resultText = ''

      const predicted = this.predictPlayer()

      window.setTimeout(() => {
        const counters = Object.keys(this.moves).filter((key) => this.moves[key].beats.includes(predicted))
        this.aiMove = counters.length && Math.random() < 0.78
          ? counters[Math.floor(Math.random() * counters.length)]
          : this.randomMove()

        this.playerHistory.push(move)

        const result = move === this.aiMove
          ? 'draw'
          : this.moves[move].beats.includes(this.aiMove)
            ? 'win'
            : 'lose'

        if (result === 'win') this.wins++
        else if (result === 'lose') this.losses++
        else this.draws++
        this.totalGames++
        if (result === 'win') this.matchWins++
        else if (result === 'lose') this.matchLosses++

        this.outcome = result
        this.resultText = result === 'win'
          ? '// YOU OUTPLAYED THE MODEL'
          : result === 'lose'
            ? '// N.E.M.O. ADAPTED'
            : '// MATCH DRAW — REASSESSING'

        this.history.unshift({
          round: this.round,
          you: move,
          ai: this.aiMove,
          label: result === 'win' ? 'PLAYER WIN' : result === 'lose' ? 'AI WIN' : 'DRAW',
          class: result === 'win' ? 'win-text' : result === 'lose' ? 'lose-text' : 'draw-text',
        })

        this.round++
        this.thinking = false

        if (this.matchTarget && (this.matchWins >= this.matchTarget || this.matchLosses >= this.matchTarget)) {
          const playerWon = this.matchWins >= this.matchTarget
          this.matchComplete = true
          this.resultText = playerWon
            ? `// YOU WIN — BEST OF ${this.matchFormat}`
            : `// N.E.M.O. WINS — BEST OF ${this.matchFormat}`
          this.matchTimer = window.setTimeout(() => {
            this.matchNumber++
            this.matchWins = 0
            this.matchLosses = 0
            this.matchComplete = false
            this.playerMove = null
            this.aiMove = null
            this.outcome = ''
            this.resultText = ''
            this.matchTimer = null
            this.showToast(`MATCH ${this.matchNumber} STARTED`)
          }, 2400)
        }

        if (this.playerHistory.length === 3) {
          this.showToast('PATTERN SCAN COMPLETE — MODEL ADAPTING')
        }
      }, 450 + Math.random() * 350)
    },
    reset() {
      this.playerMove = null
      this.aiMove = null
      this.outcome = ''
      this.resultText = ''
      this.wins = 0
      this.losses = 0
      this.draws = 0
      this.totalGames = 0
      this.matchNumber = 1
      this.matchWins = 0
      this.matchLosses = 0
      this.matchComplete = false
      if (this.matchTimer) window.clearTimeout(this.matchTimer)
      this.matchTimer = null
      this.round = 1
      this.history = []
      this.playerHistory = []
      this.showToast('SESSION RESET — MODEL MEMORY CLEARED')
    },
  },
  beforeUnmount() {
    if (this.matchTimer) window.clearTimeout(this.matchTimer)
    if (this.toastTimer) window.clearTimeout(this.toastTimer)
  },
}
</script>
