<p>
A minimax chess AI with alpha beta pruning made using chess.js and vue-chessboard...
</p>

The next steps (from my perspective) would be:

1. Improve evaluation function: with perfect evaluation function low depth is necessary, most chess engines have piece * position evaluation tables, where each type of piece has an 8 * 8 grid of numerical values, that basically instruct each piece to go to good squares for it - knights to middle, bishops to long diagonal but not quite in the corner, pawns table is interesting and hard to describe - with these 'instruction' weights lower than material value so that tactics prevail. https://www.chessprogramming.org/Piece-Square_Tables

2. Go deep on forcing lines: High depth accross the board is not achievable, branch factor explosion. But we don't need to evaluate every response, just responses that have a possibility of countering opponent and vice versa. Cut move exploration down to moves that - give check, attack a higher value piece, take a piece, other forcing moves - both ways and go very deep on this and engine will spot tactics to employ/avoid from verrry far. 

3. Pair the above together, (1) giving gentle direction and (2) giving some venom. Taking the top 5 or so things that (1) wants to do from its extremely low depth, high width 'grandmasters approved wisdom' tree, and feed them to (2) to spot tactics. If (2) finds something juicy or terrifying, act appropriately, otherwise go with (1)s recommendation.

:)

npm i <br>
npm run serve
