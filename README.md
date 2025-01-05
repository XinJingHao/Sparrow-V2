<div align="center">
  <a ><img width="300px" height="auto" src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V0/LOGO%20sparrow.jpg"></a>
</div>

## Sparrow-V2: A Reinforcement Learning Friendly Mobile Robot Simulator for Dynamic Environments

<p align="center">
  <img src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V2/case2.gif" width="250" />
  <img src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V2/case_v2.gif" width="250" />
  <img src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V2/play.gif" width="250" />
</p>

<div align=center>
  <img src="https://img.shields.io/badge/Python-blue" />
  <img src="https://img.shields.io/badge/DRL-blueviolet" />
  <img src="https://img.shields.io/badge/MobileRobot-ff69b4" />
</div>


<br>

## Installation

The dependencies for Sparrow-V2 are:

```bash
torch>=2.2.0 # GPU version is recommended
torchaudio>=2.2.0
torchvision>=0.17.0
numpy>=1.24.4
scipy>=1.10.1
pygame>=2.5.2
```

You can install **torch** by following the guidance from its [official website](https://pytorch.org/get-started/locally/). We strongly suggest you choose **CUDA>=12.4**, though CPU version or lower CUDA version is also supported.

Then you can install other packages via:

```bash
pip3 install numpy==1.24.4 scipy==1.10.1 pygame==2.5.2
```

Additionally, we recommended `python>=3.11.10`. Although other versions might also work. 


<br>

## Quick Start

### Play with Keyboard
You can play with Sparrow with your **keyboard (⇠/⇡/⇣/⇢)** to test if you have installed it successfully:

```bash
python play_with_keyboard.py
```

### Play with trained model using your mouse
```bash
python play_with_mouse.py
```

### Basic usage
```python
from Sparrow_V2 import Sparrow
import torch

envs = Sparrow()
s0 = envs.reset()
while True:
    # action_dim: dimension of action, N: number of vectorized envs, dvc: running device of Sparrow
    a = torch.randint(low=0, high=envs.action_dim, size=(envs.N,), device=envs.dvc)
    s_next, r, terminated, truncated, info = envs.step(a)
```

<br>

## Features

- **[Vectorizable](https://www.gymlibrary.dev/content/vectorising/)** (Super fast data collection)
- **High Diversity** (Randomly-generated static obstacles, Randomly moving dynamic obstacles)
- **Easy Sim2Real** (Domain randomization and sensor noise are integrated)
- **Discrete&Continuous Action** (Discrete and Continuous action are both accepted)
- **Lightweight** (Consume only a few GPU memories for vectorized environments)
- **Standard Gym API with [Pytorch](https://pytorch.org/) data flow**
- **GPU/CPU are both acceptable** (Fast & Compatible)
- **Conversion-free data flow** (The state generated by Sparrow are in _torch.tensor_ format. If you use Pytorch to build your DRL model, you can run your RL model and Sparrow both on GPU, obviating the need to transfer the data from CPU to GPU.)
- **Easy to use** (Python files only. Just import, never worry about installation)
- **Ubuntu/Windows/MacOS** are all supported
- **Accept image as map** (Customize your own environments easily and rapidly)

<br>

## Train DRL agent with Sparrow
Please refer to this [repo](https://github.com/XinJingHao/ColorDynamic).

<br>

## Dive into Sparrow

### Customize your own Sparrow:
```python
from Sparrow_V2 import Sparrow, str2bool
import argparse
import torch

'''Hyperparameter Setting for Sparrow'''
parser = argparse.ArgumentParser()
parser.add_argument('--dvc', type=str, default='cpu', help='running device of Sparrow: cuda / cpu')
parser.add_argument('--render_mode', type=str, default='human', help=" None or 'human' ")
parser.add_argument('--action_type', type=str, default='Discrete', help='Action type: Discrete / Continuous')
parser.add_argument('--window_size', type=int, default=800, help='size of the map')
parser.add_argument('--D', type=int, default=400, help='maximal local planning distance')
parser.add_argument('--N', type=int, default=1, help='number of vectorized environments')
parser.add_argument('--O', type=int, default=3, help='number of obstacles in each environment')
parser.add_argument('--RdON', type=str2bool, default=False, help='whether to randomize the Number of dynamic obstacles')
parser.add_argument('--ScOV', type=str2bool, default=False, help='whether to scale the maximal velocity of dynamic obstacles')
parser.add_argument('--RdOV', type=str2bool, default=True, help='whether to randomize the Velocity of dynamic obstacles')
parser.add_argument('--RdOT', type=str2bool, default=True, help='whether to randomize the Type of dynamic obstacles')
parser.add_argument('--RdOR', type=str2bool, default=True, help='whether to randomize the Radius of obstacles')
parser.add_argument('--Obs_R', type=int, default=14, help='maximal obstacle radius, cm')
parser.add_argument('--Obs_V', type=int, default=30, help='maximal obstacle velocity, cm/s')
parser.add_argument('--MapObs', type=str, default=None, help="name of map file, e.g. 'map.png' or None")
parser.add_argument('--ld_a_range', type=int, default=270, help='max scanning angle of lidar (degree)')
parser.add_argument('--ld_d_range', type=int, default=300, help='max scanning distance of lidar (cm)')
parser.add_argument('--ld_num', type=int, default=27, help='number of lidar streams in each world')
parser.add_argument('--ld_GN', type=int, default=3, help='how many lidar streams are grouped for one group')
parser.add_argument('--ri', type=int, default=0, help='render index: the index of world that be rendered')
parser.add_argument('--basic_ctrl_interval', type=float, default=0.1, help='control interval (s), 0.1 means 10 Hz control frequency')
parser.add_argument('--ctrl_delay', type=int, default=0, help='control delay, in basic_ctrl_interval, 0 means no control delay')
parser.add_argument('--K', type=tuple, default=(0.55,0.6), help='K_linear, K_angular')
parser.add_argument('--draw_auxiliary', type=str2bool, default=False, help='whether to draw auxiliary infos')
parser.add_argument('--render_speed', type=str, default='fast', help='fast / slow / real')
parser.add_argument('--max_ep_steps', type=int, default=500, help='maximum episodic steps')
parser.add_argument('--noise', type=str2bool, default=True, help='whether to add noise to the observations')
parser.add_argument('--DR', type=str2bool, default=True, help='whether to use Domain Randomization')
parser.add_argument('--DR_freq', type=int, default=int(3.2e3), help='frequency of Domain Randomization, in total steps')
parser.add_argument('--compile', type=str2bool, default=False, help='whether to use torch.compile to boost simulation speed')
opt = parser.parse_args()
opt.dvc = torch.device(opt.dvc)

envs = Sparrow(**vars(opt))
s, info = envs.reset()
while True:
    a = torch.randint(low=0, high=envs.action_dim, size=(envs.N,), device=envs.dvc)
    s_next, r, terminated, truncated, info = envs.step(a)
```

Note that Sparrow-V2 runs in a vectorized manner, thus the dimension of **s, a, r, terminated, truncated** are **(N,*), (N,), (N,), (N,), (N,)** respectively, where **N** is the number of vectorized environments. In addition, Sparrow-V2 has its own AutoReset mechanism. Users only need to reset the envs once in the beginning.

### Basic Robot Information:

The LiDAR perception range is 300cm×270° in this example, with an accuracy of 3 cm. The radius of the robot is 9 cm, and its collision threshold is 14 cm. 


<img src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V0/basic_robot_info.svg" align="right" width="25%"/>

The maximum linear and angular velocity of the robot is 50 cm/s and 2 rad/s, respectively. The control frequency of the robot is 10Hz (```basic_ctrl_interval```). And we use a simple but useful model to describe the kinematics of the robot

$$[V^{i+1}\_{linear},\ V^{i+1}\_{angular}] = [K_l,\ K_a]·[V^{i}\_{linear},\ V^{i}\_{angular}]+(1-[K_l,\ K_a])·[V^{target}\_{linear},\ V^{target}\_{angular}]$$

$$[dx^{i+1},dy^{i+1},\theta^{i+1}] = [dx^{i},dy^{i},\theta^{i}] + [V^{i+1}\_{linear},V^{i+1}\_{linear},\ V^{i+1}\_{angular}]·\Delta t · [\cos(\theta^{i}), -\sin(\theta^{i}), 1]$$

Here, $$[K_l,\ K_a]$$ are two hyperparameters between (0,1), describing the combined effect of inertia, friction and the underlying velocity control algorithm, default: **[0.55,0.6]**. The parameters mentioned in this section can be found in the *Car initialization* and *Lidar initialization* part of `Sparrow_V2.py` and customized according to your own scenario.

### LiDAR Group:
<img src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V1/ld_group.jpg" align="right" width="23%"/>

In DRL-based navigation, it is common that the dimension of the LiDAR state is far larger than the dimension of the robot state (e.g. the location, orientation, and speed). To prevent the LiDAR state from submerging the robot state, we provide a useful LiDAR group option (dowm-sampling). The raw LiDAR output will be grouped every ```ld_GN``` streams, and each group outputs its **minimal** distance.

```python
# Example of ld_num=12, ld_GN=2:
raw_lidar_output = [100,100, 40,60, 90,100, 100,100, 100,100, 100,100]

grouped_lidar_output = [100, 40, 90, 100, 100, 100]
```

### RL representation:
As illustrated below (left), the basic task in Sparrow is about navigating the robot to the green target area as soon as possible, without colliding with obstacles (RED:Dynamic obstacles, BLACK:Static obstacles). The position of the robot and target point will be reseted when _reaching target_ / _collide_ / _exceed maximum episodic steps_. The obstacles will be regenerated when calling the ```reset()``` function.

<img src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V2/example.png" width="25%" height="auto"><img src="https://github.com/XinJingHao/SparrowDynamic-Unreleased/blob/main/Sparrow-V2.1-Unreleased/Images/State.png" width="70%" height="auto">


#### State:

The state of the robot is a vector of length 17, $S=[V_l^{c_{tgt}}, V_{\theta}^{c_{tgt}}, V_l^{r_{tgt}}, V_{\theta}^{r_{tgt}}, D2T, \alpha, V_l, V_{\theta}, ld_0, ..., ld_{8}]$:
-  $V_l^{c_{tgt}}$ : the current target linear velocity generated by the agent. It is normalized by the maximal linear velocity of the robot (50 cm/s), thus $\in [-1,1]$
-  $V_{\theta}^{c_{tgt}}$ : the current target angular velocity generated by the agent. It is normalized by the maximal angular velocity of the robot (2 rad/s), thus $\in [-1,1]$
-  $V_l^{r_{tgt}}$ : the real target linear velocity recieved by the robot (recall control delay). It is normalized by the maximal linear velocity of the robot (50 cm/s), thus $\in [-1,1]$
-  $V_{\theta}^{r_{tgt}}$ : the real target angular velocity recieved by the robot (recall control delay). It is normalized by the maximal angular velocity of the robot (2 rad/s), thus $\in [-1,1]$
-  $D2T$ : the normalized distance from the robot to the target. The distance is normalized by the radius of the map, thus $D2T \in [0,1]$
-  $\alpha$ : the normalized relative orientation of the robot, $\alpha \in [-1,1]$
-  $V_l$ : the normalized real linear velocity of the robot. It is normalized by the maximal linear velocity of the robot (50 cm/s), thus $V_l \in [-1,1]$
-  $V_{\theta}$ : the normalized real angular velocity of the robot. It is normalized by the maximal angular velocity of the robot (2 rad/s), thus $V_{\theta} \in [-1,1]$
-  $ld_0, ..., ld_{8}$ : the grouped and normalized LiDAR result. The LiDAR result will be normalized by the maximal perception distance (300 cm), thus $ld \in [0,1]$. Note that here in this example, ```--ld_num=27``` and ```--ld_GN=3```.


#### Discrete Action:

There are 8 discrete actions avaliable in Sparrow-V2, controlling the target velocity $[V^{target}\_{linear},\ V^{target}\_{angular}]$ of the robot:

- **0 >> Turn Left:** [ 10 cm/s, 2 rad/s ]
- **1 >> Turn Left + Move Forward:** [ 50 cm/s, 2 rad/s ]
- **2 >> Move Forward:** [ 50 cm/s, 0 rad/s ]
- **3 >> Turn Right + Move Forward:** [ 50 cm/s, -2 rad/s ]
- **4 >> Turn Right:** [ 10 cm/s, -2 rad/s ]
- **5 >> Move Backward:** [ -50 cm/s, 0 rad/s ]
- **6 >> Slow Down:** [ 5 cm/s, 0 rad/s ]
- **7 >> Stop:** [ 0 cm/s, 0 rad/s ]

We strongly suggest not using the **Stop** action when training an RL model, because it may result in the robot standing still and generating low-quality data. Thus, the action space of Sparrow-V2 is **7** by default. You might have also noted that when the robot is turning left or right, we also give it a small linear velocity. We do this to help the robot escape from the deadlock.

#### Continuous Action:
If you wish to use continuous action to control the robot, you can set ```--action_type``` to ```Continuous```. Then, the action should be a _torch.tensor_ of shape ```(N,2)``` between _(-1,1)_, controling the linear/angular velocity of _N_ vectorized environments. Note that the maximal linear/angular velocity are still _50cm/s_ and _2rad/s_.

#### Reward:

<img src="https://github.com/XinJingHao/SparrowDynamic-Unreleased/blob/main/Sparrow-V2.1-Unreleased/Images/Ro.png" align="right" width="23%"/>

The reward function of Sparrow-V2 is:
- $r=200$, when arriving at the target point
- $r=-200$, when colliding with obstacle
- $r=-200$, when exceeding the maps
- $r= 0.5 \cdot r_d + r_a \cdot r_o - 0.5 \cdot r_p - 0.5$, otherwise


Here, $r_d = (D2T_{t-1}-D2T_{t})/{V_{l}^{max}}$, where $V_{l}^{max}$ is the maximal linear velocity, $D2T_{t-1}$ and $D2T_{t}$ are the distance to target point at previous and current step, respectively.
$r_a=1$, if the current action is _Move Forward_, otherise $r_a=0$. This signal is devised to encourage the robot to move as fast as possible. The definition of $r_o$ is present in the right. 
$r_p=1$, if the current action is _Move Backward_ or _Slow Down_, otherise $r_p=0$. This signal is used to punish actions that slow down the target reaching.

#### Termination:

The episode would be terminated when:
- the robot collides with the obstacles
- the robot reaches the target area
- ```D2T``` > ```D``` (the distance to target point > maximal local planning distance) 


#### Truncation:

The episode would be truncated only when the episode steps exceed `--max_ep_steps`. 

#### Info:
The absolute location and orientation of the robot can be derived from the ```info``` yielded by the ```step()``` and ```reset()``` function. The absolute location and orientation coordinate system is illustrated bellow:

<div align="center">
<img width="35%" height="auto" src="https://github.com/XinJingHao/SparrowDynamic-Unreleased/blob/main/Sparrow-V2.1-Unreleased/Images/coordinate_frames.svg">
</div>

### Render:
If `render_mode=None`, Sparrow would run at its maximum simulation speed (depending on the hardware). However, if `render_mode='human'`, there would be three options regarding the simulation speed:

- `render_speed == 'fast'`: render the Sparrow in a pygame window with maximum FPS
- `render_speed == 'slow'`: render the Sparrow in a pygame window with 30 FPS. Might be useful when debugging.
- `render_speed == 'real'`: render the Sparrow in a pygame window with **1/ctrl_interval** FPS, in accordance with the real world speed.

You can configure ```--ri``` to render different maps of the vectorized envs.



### AutoReset:

The environment copies inside the vectorized environment may be done (terminated or truncated) in different timesteps. Consequently, it is inefficient or even improper to call the *env.reset()* function to reset all copies whenever one copy is done, necessitating the design of **AutoReset** mechanism, which is illustrated below:

<div align="center">
<img width="100%" height="auto" src="https://github.com/XinJingHao/Images/blob/main/Sparrow_V1/AutoReset.svg">
</div>

**Note:**

- the AutoReset mechanism of Sparrow-V2 is the same as [V1](https://github.com/XinJingHao/Sparrow-V1) but different from [V0](https://github.com/XinJingHao/Sparrow-V0)
  
- different environment copies are reset independently

<br>

## The Sparrow families
- [Sparrow-V1](https://github.com/XinJingHao/Sparrow-V1): Single Robot, Static environments
- [Sparrow-V2](https://github.com/XinJingHao/Sparrow-V2): Single Robot, Dynamic/Static environments
- [Sparrow-V3](https://github.com/XinJingHao/Sparrow-V3): Multiple/Single Robot, Dynamic/Static environments


<br>

## Citing the Project

To cite this repository in publications:

```bibtex
@article{ColorDynamic2025,
  title={XXXX},
  author={Jinghao Xin},
  journal={XXX},
  year={2025}
}
```



## Writing in the end

The name "Sparrow" actually comes from an old saying *“麻雀虽小，五脏俱全.”* 

Hope you enjoy using Sparrow! 

Additionally, we have made detailed comments on the source code (`Sparrow_V2.py`) so that you can modify Sparrow to fit your own problem. But only for non-commercial purposes, and all rights are reserved by [Jinghao Xin](https://github.com/XinJingHao).






