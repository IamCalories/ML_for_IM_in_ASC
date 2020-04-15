

# ML_for_IM_in_ASC
Matlab code for Interference Management in Aerial Small Cells

## Start from the main.m
In the main.m, we compare four methods: 
1. Exhaustive (窮舉法): We list all possible state and find the best solution. 
2. APC (Affinity Propagation Clustering): An algorithm usually used in the clusteing problem. 
3. RL (Reinforcement Learning): build q-table to find the optimal solution. (we train the model first(offline learning)) 
4. APC + RL: we use APC to decide BS on/off, and use RL to decide the power of "on" BS (we have differnent power level) 

## System_parameter: 

## RL: 
Our objective is to "maximize the SINR".  
<img src="http://latex.codecogs.com/gif.latex?SINR = \frac{RSRP _signal}{RSRP _interference + Noise}" />  


state:   
action:  
reward:  
 
### Training: 
1. Choose action 
2. Update state 
3. RL_learn

 
## choose action 
