So far, the heuristic has guided the agent significantly. Here, we will use **reinforcement learning** to build an agent without a heuristic.

---
It is difficult to come up with a perfect heuristic as opposed to a systematic way to improve the agent. So, we will replace the heuristic with a neural network. The network accepts the board as an input, and outputs a probability for each possible move. The agent will then select a move by sampling from these probabilities.

---
Here's the practical approach:
- After each move, give the agent a reward to tell it how well it did
    - If it wins, `+1`
    - If it plays an invalid move, `-10`
    - If the opponent wins the game in its next move, `-1`
    - Else, `1/42`
- At the end of each game, the agent adds up its reward. The sum of reward is the **cumulative rewards**.

Our goal is to find the weights of the neural network that, on average, maximize the agent's cumulative rewards.

---
There are many different reinforcement learning algorithms, but they use a similar process to produce an agent
- Initially, all weights are randomly set
- As the agent plays, the algorithm tries new values for the weights to see how the cumulative rewards is affected, on average. Over time, this gives a good idea of how the weights affect cumulative reward, and the algorithm settles towards weights that performed better.
- This way, the agent tries to win the game (to get `+1`) AND avoids losing by trying to survive (to get continual `+1/42`)

---
This will implement the Stable-Baselines3 RL algorithm:

```python
import random
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline

import gym
from kaggle_environments import make, evaluate
from gym import spaces

class ConnectFourGym(gym.Env):
    def __init__(self, agent2="random"):
        ks_env = make("connectx", debug=True)
        self.env = ks_env.train([None, agent2])
        self.rows = ks_env.configuration.rows
        self.columns = ks_env.configuration.columns
        # Learn about spaces here: http://gym.openai.com/docs/#spaces
        self.action_space = spaces.Discrete(self.columns)
        self.observation_space = spaces.Box(low=0, high=2, 
                                            shape=(1,self.rows,self.columns), dtype=int)
        # Tuple corresponding to the min and max possible rewards
        self.reward_range = (-10, 1)
        # StableBaselines throws error if these are not defined
        self.spec = None
        self.metadata = None
    def reset(self):
        self.obs = self.env.reset()
        return np.array(self.obs['board']).reshape(1,self.rows,self.columns)
    def change_reward(self, old_reward, done):
        if old_reward == 1: # The agent won the game
            return 1
        elif done: # The opponent won the game
            return -1
        else: # Reward 1/42
            return 1/(self.rows*self.columns)
    def step(self, action):
        # Check if agent's move is valid
        is_valid = (self.obs['board'][int(action)] == 0)
        if is_valid: # Play the move
            self.obs, old_reward, done, _ = self.env.step(int(action))
            reward = self.change_reward(old_reward, done)
        else: # End the game and penalize agent
            reward, done, _ = -10, True, {}
        return np.array(self.obs['board']).reshape(1,self.rows,self.columns), reward, done, _

# Create ConnectFour environment 
env = ConnectFourGym(agent2="random")

import torch as th
import torch.nn as nn

!pip install "stable-baselines3"
from stable_baselines3 import PPO 
from stable_baselines3.common.torch_layers import BaseFeaturesExtractor

# Neural network for predicting action values
class CustomCNN(BaseFeaturesExtractor):
    
    def __init__(self, observation_space: gym.spaces.Box, features_dim: int=128):
        super(CustomCNN, self).__init__(observation_space, features_dim)
        # CxHxW images (channels first)
        n_input_channels = observation_space.shape[0]
        self.cnn = nn.Sequential(
            nn.Conv2d(n_input_channels, 32, kernel_size=3, stride=1, padding=0),
            nn.ReLU(),
            nn.Conv2d(32, 64, kernel_size=3, stride=1, padding=0),
            nn.ReLU(),
            nn.Flatten(),
        )

        # Compute shape by doing one forward pass
        with th.no_grad():
            n_flatten = self.cnn(
                th.as_tensor(observation_space.sample()[None]).float()
            ).shape[1]

        self.linear = nn.Sequential(nn.Linear(n_flatten, features_dim), nn.ReLU())

    def forward(self, observations: th.Tensor) -> th.Tensor:
        return self.linear(self.cnn(observations))

policy_kwargs = dict(
    features_extractor_class=CustomCNN,
)
        
# Initialize agent
model = PPO("CnnPolicy", env, policy_kwargs=policy_kwargs, verbose=0)

# Train agent
model.learn(total_timesteps=60000)

def agent1(obs, config):
    # Use the best model to select a column
    col, _ = model.predict(np.array(obs['board']).reshape(1, 6,7))
    # Check if selected column is valid
    is_valid = (obs['board'][int(col)] == 0)
    # If not valid, select random move. 
    if is_valid:
        return int(col)
    else:
        return random.choice([col for col in range(config.columns) if obs.board[int(col)] == 0])

# Create the game environment
env = make("connectx")

# Two random agents play one game round
env.run([agent1, "random"])

# Show the game
env.render(mode="ipython")
```
Note, this agent will only be trained to beat the random agent. To change this, you would need to self-play during training!