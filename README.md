# DTI Scheduling using RL

OpenAi Gym is a toolkit for reinforcement learning research. It provides a common interface to directly compare the performance results of different RL algorithms.
NS3Gym offers the possibility to integrate this framework into the network simulator ns-3 by providing an interface between the two.
This interface takes care for the management of the ns3 simulation process life cycle as well as delivering state and action information between the gym agent and the simulation environment.

## Installation

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
3. Configure and build ns-3 project:
```
# Opengym Protocol Buffer messages (C++ and Python) are build during configure
CXXFLAGS+=-Wno-deprecated ./waf configure --disable-examples --disable-tests --disable-python --enable-modules='applications','core','internet','point-to-point','wifi','opengym'
./waf build
```

4. Install ns3gym located in src/opengym/model/ns3gym (Python3 required)
```
pip3 install ./src/opengym/model/ns3gym
```



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


For any finite Markov decision process (FMDP), Q-learning finds a policy that is optimal in the sense that it maximizes the expected value of the total reward over any and all successive steps, starting from the current state

The script is divided in a traning phase and a test phase.




## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.
