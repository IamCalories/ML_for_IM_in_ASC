# ML_for_IM_in_ASC
Matlab code for Interference Management in Aerial Small Cells
---  

## Our System Architecture  
> - In our system, we assume that there are 12 BSs and 60 UEs.  
> - Each BS serves 5 UEs [all the time]. We assume that 5 UEs(it is seemed as 1 group) will always go toward the same direction. Also, that BS will be in the center of each group.  
> - If there is any two BSs getting closer to each other and hence cause servere interference. We will determine to close one of them. In other words, there will be only one BS to serve these 10 UEs(2 group). But the closed BS is still in the center of the original group.   

---  

## Start from the main.m
In the main.m, we compare four methods: 
1. Exhaustive (窮舉法): We list all possible state and find the best solution. 
2. APC (Affinity Propagation Clustering): An algorithm usually used in the clusteing problem. 
3. RL (Reinforcement Learning): build q-table to find the optimal solution. (we train the model first(offline learning)) 
4. APC + RL: we use APC to decide BS on/off, and use RL to decide the power of "on" BS (we have differnent power level) 

---  

## RL:  
### Training:
> 1. Choose the action. (choose_action.m) 
> 2. According to the action we choose, we update the state. (update_state.m)
> 3. Update the q_table. (RL_learn.m)  

### Testing:  
> 1. Choose the action. (choose_action.m) 
> 2. According to the action we choose, we update the state. (update_state.m)  

### Details:  

#### Our objective is to "maximize the SINR".  

>>>>>  <img align="center" src="http://latex.codecogs.com/gif.latex? SINR = \frac{signalRSRP}{interferenceRSRP + Noise}" />  

 
1. RSRP (Reference Symbol Received Power) is "the signal strength" that UE received from the downlink reference signal.    
> - In our project, in every single time, UE will send RSRP which it received in the last time back to the BS.    
> - Each UE chooses "the strongeset strengest signal" among all BS (in our system: 12 BSs) to be the BS who serves it.  
We called that strongeset strengest signal "signal RSRP". Otherwise, "interference RSRP".  
 
#### state:   
> - Since our objective is to maximize the SINR, where SINR is proportion to the RSRP received by UE. Also, RSRP is propotion to the distance between BS and UE and the transmission power transmitting from the BS. Therefore, we have two parameters in our state. The first one is all the locations of BS in our system. The second one is the transmission power of each BS.  
> - state = [the location of BS(12), the transmission power of BS(12)]  
> - We use state_list to store the state which has shown.

#### action:  
> - action_list = 1 : group x power_level, where group number is 12.
> - According to the power_level of our system.(in file power_parameter.m.)  
> - ex: If the power level = 2, it means that there are two power level on (1W) and off (0W).  
> the action_list will be 1:24, where 1:12 is to change one of the BS to off, and 13:24 is to change one of the BS to on.  
> - ex: If the power level = 3, it means that there are three power level on (1W), half on (0.5W), and off (0W).  
> the action_list will be 1:36, where 1:12 is to change one of the BS to off, 13:24 is to change one of the BS to half on,and 25:36 is to change one of the BS to on.


#### reward:  
> - Since we want to find the optimal state which can maximize the SINR, we set the reward proportional to the SINR. The larger the SINR, the more likely being choosen in the next time.  

---  

## Simulation Result:  
1. Consider power level = 2 [off, on]   
- If we don't control the power level, all BSs are on. The capacity is 223 Mbps in average.  
- If we use exhaustive, the capacity is 258 Mbps in average, and it spends 2384s to calculate the result.  
- If we use APC algorithm, the capacity is 240 Mbps in average and it spends 1.5s to calculate the result.  
- If we use RL, the capacity depends on how many times it spends.  
|time(s)|capacity(Mbps)|  
| :-: |    :-:  |
|2.5    |230           |

> - If we set the optimal solution (exhaustive method) to 100% [capacity from 223 to 258], then using APC can achieve 50% [capacity from 223 to 240]. 

2. Consider power level = 3 [off, half on, on]   
- If we don't control the power level, all BSs are on. The capacity is 223 Mbps in average.  
- If we use exhaustive, the capacity is 258 Mbps in average, and it spends 2384s to calculate the result.  
- If we use APC algorithm, the capacity is 240 Mbps in average and it spends 1.5s to calculate the result.  
- If we use RL, the capacity depends on how many times it spends, and so is APC+RL.  

> - If we set the optimal solution (exhaustive method) to 100% [capacity from 223 to 258], then using APC can achieve 50% [capacity from 223 to 240].  

3. We use q-table to store the state-action transition probability. Therefore, the larger the power level, the larger the number of state-list and q-table size.  

(Is it necessary to have many power level ????)
> - We can observe from the exhaustive method that if we increase the number of power level from 0 to 1 (all on to off/on), we can increase 35 Mbps. However, if we increase the number of power level from 1 to 2 (off/on to off/0.5on/on), we can only increase 2 Mbps.
> - Since we need to find the state in the larger state-list in power level=3 than power-level = 2, it will spend more time but doesn't get benefits.



