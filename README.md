# Deep RL Navigation

Deep Reinforcement Learning for mobile robot navigation in ROS2 Gazebo. A robot learns to reach random goal points while avoiding obstacles, using TD3 (Twin Delayed DDPG). Built with ROS2 Humble, Gazebo 11, and PyTorch.

### TD3 architecture
![TD3 architecture](https://miro.medium.com/1*b3l5KUz9X60QA8Iy95dBwA.png)

### Environment details

- **State (24-d):** 20 LiDAR sector distances (min range per angular bin, ~180° front arc) + distance-to-goal + heading-error-to-goal + last linear/angular velocity applied.
- **Action (2-d):** linear velocity `[0, 1]` (forward only) and angular velocity `[-1, 1]`.
- **Reward:** `+100` on reaching goal, `-100` on collision, otherwise `action[0]/2 - |action[1]|/2 - obstacle_proximity_penalty/2` rewards forward motion, penalizes sharp turns and getting close to obstacles.

### Usage

**Train:**
```bash
ros2 launch td3 training_simulation.launch.py
```

**Test:**
```bash
ros2 launch td3 test_simulation.launch.py
```
## Troubleshooting

**Problem:** Sometimes Gazebo11 crashes, causing the RL training node to get stuck.

**Solution:**

1. **Source Gazebo's own setup script.** After sourcing your ROS2 workspace, also run:
```bash
   source /usr/share/gazebo/setup.sh
   # or
   source /usr/share/gazebo-11/setup.sh
```
   This sets the `GAZEBO_MODEL_PATH`/`GAZEBO_RESOURCE_PATH` env vars needed by the rendering engine. Missing them is a common cause of this crash.

2. **Clear the Gazebo cache:**
```bash
   rm -rf ~/.gazebo/paging ~/.gazebo/models/.database.cache
```
   Only remove the cached/state files, not the entire `~/.gazebo/models` folder. A stale or corrupted cache can cause scene initialization to fail on startup.

