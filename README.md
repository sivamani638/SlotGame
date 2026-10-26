# Unity Slot Game

A retro pixel-art three-reel slot machine built in Unity 6 for the Underpin Technology Unity Developer Intern assignment.

## Game Overview
- Three reels with four symbols: 7, Cherries, Bell, BAR (supplied assets, pixel-art look preserved).
- **Win condition:** the player wins only when **all three reels show the same symbol**. Two matching symbols pay nothing.
- Simulated credits only (no real money). Start with 1,000 credits; bets of 10 / 50 / 100 selected with the arrow buttons.
- Payout = bet x multiplier (total return; the bet is deducted when the spin starts):

| Symbol | Multiplier |
|---|---|
| 7 | 30x |
| Bell | 15x |
| Cherries | 10x |
| BAR | 5x |

With four equally likely symbols, any specific triple has a 1/64 chance, so the theoretical return to player is (30+10+15+5)/64 = 93.75%.
- X button opens a "Reset game?" confirmation (uses the supplied popup and YES/NO buttons). Running out of credits offers a restart automatically.

## Instructions to Run the WebGL Build
Browsers block WebGL builds opened directly from disk, so serve the folder:
1. Open a terminal in `Build/WebGL`.
2. Run `python -m http.server 8000`.
3. Open `http://localhost:8000` in a browser.

(The build is uncompressed so it works on any static host, e.g. GitHub Pages.)

## Bonus Features
None yet.

## Thought Process / Approach
- **Separation of concerns:** `PayoutTable` (win rule/payout) and `BalanceModel` (credits/bet) are plain C# with no Unity dependencies, so they are unit tested. `ReelController` handles one reel's animation, `UIManager` only displays and raises click events, and `SlotMachineController` coordinates the spin using a small state machine (Idle, Spinning, Evaluating, OutOfCredits).
- **RNG and fairness:** the result for every reel is chosen once, before any animation, through `IRandomSource` (Unity's `Random.Range`, upper bound exclusive, so indices are always valid). Each reel is then told which symbol to land on. The animation cannot change the outcome, and the controller double-checks that the displayed symbol matches the chosen one.
- **Reel animation:** each reel is a looping strip scrolled inside a masked window. It accelerates, cruises, then brakes with ease-out matched to the cruising speed (no jolt) and snaps to an exact cell, so symbols are always aligned. Reels stop one after another.
- **No double payouts:** spins are ignored unless the state is Idle; the bet is deducted once by `TryPlaceBet`; winnings are credited in exactly one place after all reels stop.
- **Assets:** sprite sheets are sliced by scanning the alpha channel (so rectangles come from the real pixels), and the cabinet's reel windows are detected from its transparent holes.

## Project Structure
```
Assets/
  Scripts/   Core (PayoutTable, BalanceModel, IRandomSource), Reels, UI, SlotMachineController
  Editor/    SlotGameSetup (menu: Slot Game)
  Tests/     EditMode unit tests
  Sprites/   Backgrounds, Machine, Symbols, Buttons, UI (created by the setup tool)
  Scenes/    SlotMachine.unity
  Prefabs/ Animations/ UI/ Sounds/  (see notes)
Build/WebGL  WebGL build
```

## Opening the Unity Project
Unity 6 (6000.x) with Web Build Support installed. Open the project folder in Unity Hub, open `Assets/Scenes/SlotMachine.unity`, press Play.

## Tests
Window > General > Test Runner > EditMode > Run All.

## Known Limitations
- No sound; no spin lever interaction (a SPIN button is used).


