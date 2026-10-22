# Duel-Code-Practice
A practice for GitHub through dueling. 
[README.md](https://github.com/user-attachments/files/33848753/README.md)
# Yu-Gi-Oh! Duel Table

A rules engine plus a simple browser table for duelling a friend. Rules follow the **Master Rules, April 1, 2020 revision**, the latest set I could confirm. Always check Konami's current rulebook; if a rule changes, start with `src/rules.js`.

## How the "everything is legal" checking works

1. **Legal-move list.** `getLegalActions(game)` works out every move the rules allow right now. The table only shows you those moves, so you can't click an illegal one.
2. **Gatekeeper.** `dispatch(game, action)` refuses any move that isn't on that list.
3. **Audit.** The move runs on a copy of the game, then `audit()` checks the result: no cards created or lost, no duplicates, monsters only in Monster Zones, Spells/Traps only in their zones, face-down monsters in Defense, chain links point at real cards. If anything is off, the move is rejected and the real game is untouched.
4. **Tests.** `npm test` runs 16 checks, including 150 random duels that must never break the audit.

## What is implemented

Setup (8000 LP, 40-60 card decks, 3-copy limit, draw 5), Draw/Main 1/Battle/Main 2/End phases, first player skips the first draw and cannot attack on turn 1, one Normal Summon or Set per turn, Tribute Summon/Set (Level 5-6 = 1, 7+ = 2), Flip Summon, position changes, attacks and battle damage (ATK vs ATK, vs Defense, face-down flip, direct), Spell/Trap Zones and the Field Zone, Graveyard and banished zones, setting and activating Spells/Traps with set-turn rules, Spell Speeds, chains with both-players-pass resolution, a response window at phase changes, the 6-card hand limit, and winning by Life Points or deck-out.

## What is NOT implemented yet (be aware)

- Extra Deck summons (Fusion, Synchro, Xyz, Pendulum, Link) and the Extra Monster Zones.
- Trigger effects, flip effects, continuous and equip effects, effects that respond to summons or attacks (for example Mirror Force-style traps), damage-step timing, and effect negation.
- Choosing which zone a monster goes in (it uses the first free one).
- The Forbidden & Limited list. Pass the current one as `banlist` to `createGame`, for example `{ pot_of_greed: 0 }`.
- Playing over the internet. Right now both players share one screen.
- Only 10 monsters and 5 Spells/Traps are in `src/cards.js`.

Yu-Gi-Oh! has thousands of cards with unique effects, so full coverage is a very large project. Mature open-source engines exist (EDOPro's `ygopro-core` with its card scripts is the best known) if you eventually want every card.

## Run it in Cursor

1. Install Node.js (nodejs.org, the LTS version).
2. In Cursor: File > Open Folder, choose this folder.
3. Open the terminal (Ctrl+` or Cmd+`) and run `npm test`. You should see "pass 16".
4. Run `npm start`, then open http://localhost:8080 in your browser.

## Put it on GitHub

1. On github.com, click New repository, name it `ygo-duel`, leave it empty.
2. In Cursor's terminal:
   ```
   git init
   git add .
   git commit -m "First version"
   git branch -M main
   git remote add origin https://github.com/YOUR-NAME/ygo-duel.git
   git push -u origin main
   ```
3. On GitHub, open Settings > Pages, set Source to "Deploy from a branch", Branch `main`, folder `/ (root)`. After a minute, the table is live at `https://YOUR-NAME.github.io/ygo-duel/`.
4. The included workflow in `.github/workflows/test.yml` runs the tests on every push.

## Add a card

Plain monsters are just data in `src/cards.js`:
```js
my_monster: mon('Card Name', 4, 1800, 1000),
```
Then add its id to a deck in `src/decks.js`. Spells and Traps need a `resolve` function; copy `pot_of_greed` or `dust_tornado` as a pattern. Run `npm test` after each change.

Card names and stats belong to Konami. This is a personal fan project.
