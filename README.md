# ML_for_IM_in_ASC
Matlab code for Interference Management in Aerial Small Cells
  
## Our System Architecture  
> - In our system, we assume that there are 12 BSs and 60 UEs.  
> - Each BS serves 5 UEs [all the time]. We assume that 5 UEs(it is seemed as 1 group) will always go toward the same direction. Also, that BS will be in the center of each group.  
> - If there is any two BSs getting closer to each other and hence cause servere interference. We will determine to close one of them. In other words, there will be only one BS to serve these 10 UEs(2 group). But the closed BS is still in the center of the original group.   

## Start from the main.m
In the main.m, we compare four methods: 
1. Exhaustive (窮舉法): We list all possible state and find the best solution. 
2. APC (Affinity Propagation Clustering): An algorithm usually used in the clusteing problem. 
3. RL (Reinforcement Learning): build q-table to find the optimal solution. (we train the model first(offline learning)) 
4. APC + RL: we use APC to decide BS on/off, and use RL to decide the power of "on" BS (we have differnent power level) 

## System_parameter: 

## RL: 
#### Our objective is to "maximize the SINR".  

>>>>>  <img align="center" src="http://latex.codecogs.com/gif.latex? SINR = \frac{signalRSRP}{interferenceRSRP + Noise}" />  

 
1. RSRP (Reference Symbol Received Power) is "the signal strength" that UE received from the downlink reference signal.    
> - In our project, in every single time, UE will send RSRP which it received in the last time back to the BS.    
> - Each UE chooses "the strongeset strengest signal" among every BS(in our system: 12 BSs) to be the BS who serves it.  
We called that strongeset strengest signal "signal RSRP". Otherwise, "interference RSRP".  
 
#### state:   
> - Since our objective is to maximize the SINR, where SINR is proportion to the RSRP received by UE. Also, RSRP is propotion to the distance between BS and UE and the transmission power transmitting from the BS. Therefore, we have two parameters in our state. The first one is all the locations of BS in our system. The second one is the transmission power of each BS.  
> - state = [the location of BS(12), the transmission power of BS(12)]  
> - We use state_list to store the state which has shown.

#### action:  
> - action_list = 1 : group x power_level, where group number is 12.
> - According to the power_level of our system.(power_parameter.m.)  
> If the power level = 2, it means that there are two power level on(1W) and off(0W).  
> * the action_list will be 1:24, where 1:12 is to change one of the BS to off, and 13:24 is to change one of the BS to on.
> If the power level = 3, it means that there are three power level on(1W), half on(0.5W), and off(0W).  
> * the action_list will be 1:36, where 1:12 is to change one of the BS to off, 13:24 is to change one of the BS to half on,and 25:36 is to change one of the BS to on.


#### reward:  
 
### Training: 
1. Choose action 
2. Update state 
3. RL_learn

 
## choose action 
