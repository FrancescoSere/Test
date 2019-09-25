# DTI Scheduling using RL

[OpenAI Gym](https://gym.openai.com/) is a toolkit for reinforcement learning research. It provides a common interface to directly compare the performance results of different RL algorithms.
[NS3Gym](https://github.com/tkn-tub/ns3-gym) offers the possibility to integrate this framework into the network simulator ns-3 by providing an interface between the two.
This interface takes care for the management of the ns3 simulation process life cycle as well as delivering state and action information between the gym agent and the simulation environment.

## Installation of NS3Gym

1. Install all required dependencies required by ns-3.
```
# minimal requirements for C++:
apt-get install gcc g++ python

see https://www.nsnam.org/wiki/Installation
```
2. Install ZMQ and Protocol Buffers libs:
```
# to install protobuf-3.6 on ubuntu 16.04:
sudo add-apt-repository ppa:maarten-fonville/protobuf
sudo apt-get update

apt-get install libzmq5 libzmq5-dev
apt-get install libprotobuf-dev
apt-get install protobuf-compiler
```
3. Configure and build, the configure command is specific to the 802.11ad implementation. :
```
# Opengym Protocol Buffer messages (C++ and Python) are build during configure
CXXFLAGS+=-Wno-deprecated ./waf configure --disable-examples --disable-tests --disable-python --enable-modules='applications','core','internet','point-to-point','wifi','opengym'
./waf build
```

4. Install ns3gym located in src/opengym/model/ns3gym (Python3 required)
```
pip3 install ./src/opengym/model/ns3gym
```

5. Troubleshooting

If a previous installation of ns3gym is present, the install command will detect it and fail to install the newer one. In this case it's possible to force the update by adding `--update` to the previous command





## Usage

Any ns-3 simulation script can be used as a Gym environment. This requires only to instantiate `OpenGymInterface` and implement the ns3/gym C++ interface consisting of the functions listed below
```
Ptr<OpenGymSpace> GetObservationSpace();
Ptr<OpenGymSpace> GetActionSpace();
Ptr<OpenGymDataContainer> GetObservation();
float GetReward();
bool GetGameOver();
std::string GetExtraInfo();
bool ExecuteActions(Ptr<OpenGymDataContainer> action);
```
In this work, the required functions have been implemented in the `DmgNewRLScheduler` class.

The script `evaluate-rl-scheduler-v3.py` implements a QLearning approach.
Q-learning is a model-free reinforcement learning algorithm. The goal of Q-learning is to learn a policy, which tells an agent what action to take under what circumstances. It does not require a model (hence the connotation "model-free") of the environment, and it can handle problems with stochastic transitions and rewards, without requiring adaptations.
More on QLearning can be read

The script is divided in a training phase and a test phase. The results of the training phase are saved in a `QTable` structure and used to decide the best action on the test phase.

To get the simulation running, execute the `evaluate-rl-scheduler-v3.py` located in the `scratch\final_evaluation\` folder. The script saves some useful statistics, the default path is `../output/`. It is necessary to create the folder output with two additional subfolders test_episodes and train_episodes if not present or change the path.
The generated file are shown below.
```
/test_episodes
/train_episodes
-ns3-throughput.txt
-ns3-statistics.txt
-ns3-dti-occupancy.txt
```

Examples
========

All examples for NS3gym can be found [here](https://github.com/tkn-tub/ns3-gym/tree/master/scratch) while specifics OpenAI examples can be accessed [here](https://github.com/openai/gym/tree/master/examples)

## Basic Interface of NS3Gym

1. Example Python script. Note, that `gym.make('ns3-v0')` starts ns-3 simulation script located in current working directory. As specified before there `.cc` file must implement the requested methods
```
import gym
import ns3gym
import MyAgent

env = gym.make('ns3-v0')
obs = env.reset()
agent = MyAgent.Agent()

while True:
  action = agent.get_action(obs)
  obs, reward, done, info = env.step(action)

  if done:
    break
env.close()
```
