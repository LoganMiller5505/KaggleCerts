All possible outcomes can be represented in a **game tree**. It represents each possible move, by both the agent and opponent, starting with an empty board. When fully completed, it can help the agent pick the move where it is most likely to win.

---
In practice, the agent only can ever work with a small subset of the game tree when planning a move. To make sure the incomplete tree is still useful, we will use a **heuristic function**. The heuristic assigns scores to different game boards, with an estimate that boards with higher scores are more likely to result in the agent winnings the game.

---
For instance, one heuristic that might work reasonably well for Connect Four is looking at each group of four adjacent locations and assigning:
- 1000000 points if the agent has four discs in a row (game won)
- 1 point if the agent filled three spots, and the remaining spot is empty
- -100 points if the opponent filled three spots, and the remaining spot is empty

This heuristic is then used to assign a score to each board, and the board with the highest score (or randomly selected to break ties) will be selected by the agent.

---
The **one-step** lookahead agent will:
- Use the heuristic to assign a score to each possible valid move
- Select the move that gets the highest score (ties broken randomly)

```python
def agent(obs, config):

    valid_moves = [c for c in range(config.columns) if obs.board[c] == 0]

    grid = np.asarray(obs.board).reshape(config.rows, config.columns)

    scores = dict(zip(valid_moves, [score_move(grid, col, obs.mark, config) for col in valid_moves]))

    max_cols = [key for key in scores.keys() if scores[key] == max(scores.values())]

    return random.choice(max_cols)
```