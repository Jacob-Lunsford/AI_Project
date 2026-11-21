# ROSHAMBO Neural Arena

ROSHAMBO is a Rock, Paper, Scissors, Lizard, Spock game built with Vue 3 and Vite. It has a dark futuristic design using cyan, purple, pink, and gold accents.

## Features

- Five moves: Rock, Paper, Scissors, Lizard, and Spock
- N.E.M.O. adaptive AI that predicts and counters the player's moves
- Pattern-analysis confidence and a plain-language behavioral insight
- Session scoreboard for player wins, N.E.M.O. wins, and draws
- Match statistics for total games and the player's win percentage
- Best-of-3, best-of-5, best-of-7, and endless match modes
- Automatic next-match start after a best-of match is clinched
- Customizable cyan, violet, and gold arena themes
- Arena animation toggle and animated round-result feedback
- In-game settings and instructions popups
- Round counter
- Five most recent player moves
- Match log containing recent rounds
- Reset control that clears the complete session
- Desktop and mobile-friendly responsive interface

## Match statistics

The match statistics were added to the existing session-score panel:

- **Total Games** counts every completed round, including wins, losses, and draws.
- **Win %** is the player's wins divided by total games, multiplied by 100.
- The win percentage includes draws in the denominator but not in the number of wins.
- Before the first round, Win % displays `0%`.
- The Reset button resets total games and Win % along with the wins, losses, draws, round counter, recent moves, player history, arena selections, match log, and AI memory.

## Match formats and customization

Open **SETTINGS** in the top navigation to select a theme, turn arena animations on or off, or choose a match format:

- **Best of 3:** first to 2 wins
- **Best of 5:** first to 3 wins
- **Best of 7:** first to 4 wins
- **Endless:** no match target; continue playing

Draws count toward session totals but not toward either player's best-of match score. After a match is clinched, the result is shown briefly and the next match starts automatically. The cumulative scoreboard, total games, win percentage, AI pattern history, and match log remain for the session. Changing match format resets only the current match score. These appearance and match settings remain selected until the page is reloaded.

### Customization update details

The customization update changed the game in these specific ways:

- Added a **SETTINGS** navigation button and an accessible settings dialog with close button, backdrop click, and Escape-key dismissal.
- Added three selectable accent palettes: **Cyan Core**, **Violet Nebula**, and **Solar Gold**. Palette changes apply immediately to the arena and interface.
- Added an **Arena Animations** switch. Turning it off disables CSS animations and transitions, including spinning rings, thinking pulse, and result effects.
- Added a **Match Format** selector for best of 3, 5, 7, or Endless. The selector is disabled while a round or match-ending transition is in progress.
- Added a current-match number, first-to target, player and N.E.M.O. match scores, and score meters for finite match formats. Endless mode displays an endless-play indicator instead.
- Best-of matches are clinched at the required majority (2 wins for best of 3, 3 for best of 5, and 4 for best of 7). Draws advance the session game total but do not change either match score.
- After a clinch, the winner is displayed, move buttons are temporarily disabled, then the next match begins after a short pause. Session scoreboard, games played, win percentage, AI pattern learning, round counter, recent moves, and match log are retained.
- Changing match format resets only current-match progress, not the broader session. The existing **RESET** control clears both session and match progress.
- Added outcome-specific arena borders and glow, plus a winner highlight on the winning fighter.
- Added responsive settings controls and match-progress visuals for narrow/mobile screens.
- Updated the in-game instructions dialog and README to explain the match modes and draw handling.

## How to run

```text
npm install
npm run dev
```

Open the local address shown by Vite.

Build the production version with:

```text
npm run build
```

## Testing the game

1. Play one or more rounds.
2. Confirm Total Games increases after each completed win, loss, or draw.
3. Confirm Win % follows `player wins / total games x 100`.
4. Confirm a draw increases Total Games but does not increase the player's wins.
5. Select each best-of format and verify the first-to target, match announcement, and automatic next-match start. Verify Endless has no clinch.
6. Change themes and toggle animations in Settings; confirm visual changes take effect immediately.
7. Click Reset and confirm session totals, match progress, round counter, match log, recent moves, and AI pattern history reset.
8. Run `npm run build` and check the game and popups at desktop and mobile widths.

## Documentation

- The **INSTRUCTIONS** button in the top navigation opens an in-game instructions popup.
- [Project instructions](.github/copilot-instructions.md) describe the project conventions and preserve existing functionality.
