This project will focus on building agents to play Connect Four.

To see what agents have already been implemented, run:
```python
from kaggle_environments import make, evaluate

env = make("connectx", debug=True)

print(list(env.agents))
```
To make a game with two random agents playing each other:
```python
env.run(["random", "random"])

env.render(mode="ipython")
```

---
Your own agent should be implemented as a Python function that accepts two arguments: `obs` and `config`. The function returns an integer with the selected column, with indexing starting at zero (so values should be 0-6, inclusive).

Some examples of implemented agents:
```python
# Selects random valid column
def agent_random(obs, config):
    valid_moves = [col for col in range(config.columns) if obs.board[col] == 0]
    return random.choice(valid_moves)

# Selects middle column
def agent_middle(obs, config):
    return config.columns//2

# Selects leftmost valid column
def agent_leftmost(obs, config):
    valid_moves = [col for col in range(config.columns) if obs.board[col] == 0]
    return valid_moves[0]
```

---
**obs** contains two pieces of information:
- `obs.board` is the game board, or a Python list with one item for each grid location, sweeping left to right and top to bottom when visually looking at the board
- `obs.mark` is the piece assigned to the agent (either 1 or 2)

**config** contains three pieces of information:
- `config-columns` is the number of columns in the game board (`7` for Connect Four)
- `config.rows` is the number of rows in the game board (`6` for Connect Four)
- `config.inarow` is the number of pieces a player needs to get in a row in order to win (`4` for Connect Four)

---
To have custom agents play a single game round, use `env.run()`:
```python
env.run([agent_leftmost, agent_random])

env.render(mode="ipython")
```

To instead calculate the win percentage over time, use the `get_win_percentages()` function.