# ML_for_IM_in_ASC
(Matlab code for Interference Management in Aerial Small Cells)  

---  

## Our System Architecture  
> - In our system, we assume that there are 12 BSs and 60 UEs.  
> - Each BS serves 5 UEs [all the time]. We assume that 5 UEs(it is seemed as 1 group) will always go toward the same direction. Also, that BS will be in the center of each group.  
> - If there is any two BSs getting closer to each other and hence cause servere interference. We will determine to close one of them. In other words, there will be only one BS to serve these 10 UEs(2 group). But the closed BS is still in the center of the original group.   

<p align="center">
<img src="https://github.com/IamCalories/ML_for_IM_in_ASC/blob/master/sr1.png" width="30%" height="30%">
</p>  

---  

## Start from the main.m
In the main.m, we compare four methods: 
1. Exhaustive (窮舉法): We list all possible state and find the best solution. 
2. APC (Affinity Propagation Clustering): An algorithm usually used in the clusteing problem. 
3. RL (Reinforcement Learning): build q-table to find the optimal solution. (we train the model first(offline learning)) 
4. APC + RL: we use APC to decide BS on/off, and use RL to decide the power of "on" BS (we have differnent power level) 

---  

## RL  
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

## Simulation Result  

1. Consider power level = 1 [on] 
> - If we don't control the power level, **all BSs turn on**. The capacity is **223 Mbps** in average.  

  
2. Consider power level = 2 [off, on]  
> - Using three methods
>> - If we use **exhaustive**, the capacity is **441 Mbps** in average, and it spends **11s** to calculate the result.  
>> - If we use **APC**, the capacity is **400 Mbps** in average and it spends **1.2s** to calculate the result.  
>> - If we use **RL**, the capacity depends on how much time it spends.  
>> - RL:  

>>>  | time (s) | capacity (Mbps) |  
>>>  | :-: | :-: |
>>>  |2.1|360|
>>>  |6.2|367.3|
>>>  |11.6|374.2|
>>>  |18.7|381.5|
>>>  |26.8|387.1|
>>>  |35.9|390.6|
>>>  |46.6|394.9|
>>>  |57.5|397.1|
>>>  |69.1|400.2|
>>>  |82|403|
>>>  |95|404|
>>>  |108.6|407|
>>>  |122.6|408.5|
>>>  |136.5|410.2|
>>>  |150.4|410.8|
>>>  |164.7|411.6|
>>>  |178.9|411.4|
>>>  |193.3|411.6|
>>>  |207.8|412|
>>>  |222.5|413|
>>>  |237.3|413.2|
>>>  |252|413.1|
>>>  |266.5|413.9|
>>>  |281.1|414.4|
>>>  |295.9|415.7|
>>>  |310.7|415.8|
>>>  |325.5|416.4|
>>>  |340.5|416.2|
>>>  |355.3|417|
>>>  |370.2|417|
>>>  |385|417.2|
>>>  |400|419.1|
>>>  |414.9|419.2|
>>>  |430.1|419.4|
>>>  |445|419.4|
>>>  |460.3|419.3|
>>>  |475.4|419.5|
>>>  |490.3|419.3|
>>>  |505.9|419.3|
>>>  |521.2|419.4|
>>>  |536.3|419.5|
>>>  |551.7|419.3|
>>>  |567|419.3|
>>>  |582.5|419.4|
>>>  |598.2|419.5|
>>>  |613.8|419.5|
>>>  |629.3|419.5|
>>>  |644.8|419.4|
>>>  |660.4|419.7|
>>>  |676.2|419.8|

  
  
3. Consider power level = 3 [off, half on, on]   
> - Using four methods  
>> - If we use **exhaustive**, the capacity is **453 Mbps** in average, and it spends **1273s** to calculate the result.  
>> - If we use **APC**, the capacity is **400 Mbps** in average and it spends **1.2s** to calculate the result.  
>> - If we use **RL**, the capacity depends on how mauch time it spends, and so is **APC+RL**.  
>> - RL: 
>>>   | time (s) | capacity (Mbps) |  
>>>   | :-: | :-: |
>>>   |3|362.2|
>>>   |7.3|371.6|
>>>   |14.2|380|
>>>   |22.7|383.4|
>>>   |33.3|388.7|
>>>   |44.9|392.7|
>>>   |57.8|394.2|
>>>   |70.4|396.8|
>>>   |85.1|400|
>>>   |100.1|402.5|
>>>   |117.6|404.5|
>>>   |150.6|406.6|
>>>   |167.4|407.2|
>>>   |185|407.6|
>>>   |203.9|409|
>>>   |222.4|409.2|
>>>   |241.4|409.6|
>>>   |260.3|410.1|
>>>   |280.7|410.7|
>>>   |301.2|410.9|
>>>   |322.1|411.6|
>>>   |342.4|411.7|
>>>   |362.9|411.8|
>>>   |384.2|412.4|
>>>   |405.6|412.7|
>>>   |427.1|412.6|
>>>   |449.1|413.6|
>>>   |461.5|414.5|
>>>   |483|414.4|
>>>   |504.9|415|
>>>   |548.5|415|
>>>   |570.8|415|
>>>   |593.2|415.3|
>>>   |616.1|415.6|
>>>   |639.5|416.2|
>>>   |662.7|416.3|
>>>   |686.1|416.5|
>>>   |709.3|416.5|
>>>   |732.3|416.8|
>>>   |755.8|417.5|
>>>   |779.6|417.8|
>>>   |803.2|417.8|
>>>   |828.6|418.1|
>>>   |852.9|418|
>>>   |877.2|418|
>>>   |901.8|418.1|
>>>   |926|418.2|



>> - APC + RL:  
>>>   | time (s) | capacity (Mbps) |  
>>>   | :-: | :-: |
>>>   |5.5|408.8|
>>>   |13.5|410.5|
>>>   |23.3|413.3|
>>>   |33|414.9|
>>>   |43.9|417|
>>>   |55.9|419.3|
>>>   |68.1|419.5|
>>>   |80.5|420.9|
>>>   |94.5|421.6|
>>>   |108.9|421.8|
>>>   |123.5|422.2|
>>>   |138.6|422.6|
>>>   |154.2|422.4|
>>>   |170|422.8|
>>>   |185.8|423.1|
>>>   |202.9|423.3|
>>>   |219.6|424|
>>>   |238.2|424.5|
>>>   |256.5|424.8|
>>>   |275|425.3|
>>>   |293.3|425.8|
>>>   |311.8|426.1| 
>>>   |330.5|426.6|
>>>   |349.7|427.2|
>>>   |368.7|427.5|
>>>   |388.1|428|
>>>   |407.3|428|
>>>   |426.7|428|
>>>   |447.2|428|
>>>   |467.5|428|
>>>   |488.8|428.4|
>>>   |509.5|428.4|
>>>   |530.6|428.5|
>>>   |551.8|428.5|
>>>   |573.2|428.5|
>>>   |594.4|428.9|
>>>   |615.7|428.9|
>>>   |637.3|428.9|
>>>   |659.3|429.1|
>>>   |681.5|429.4|
>>>   |704.5|429.4|
>>>   |428.3|429.5|
>>>   |752.7|429.5|
>>>   |776.2|429.7|
>>>   |800|429.8|
>>>   |824|430|
>>>   |848|430|
>>>   |871.8|430|
>>>   |895.8|430.1|
>>>   |920.2|430.2|



---

## Conclusion in my opinion  
1. In power level=2,   
> - If we set the optimal solution (exhaustive method) to 100% [capacity from 223 to 256], then using APC can achieve 50% [capacity from 223 to 240]. However, APC only takes 10% of the time comparing to exhaustive method.  

  >>>   | Method | Time  | Capacity (Mbps) | 
  >>>   | :-: | :-: | :-: |
  >>>   | exhaustive |11/11 = 1|(256-223)/(256-223) = 100%|
  >>>   | APC |1.4/11 = 0.1|(240-223)/(256-223) = 50%|

2. Compare different power level ... (Is it necessary to have many power level ????)

  > - We use q-table to store the state-action transition probability. Therefore, the larger the power level, the larger the number of state-list and q-table size. Compare the RL result shown above, powerlevel=3 spends more time but doesn't get benefits.  
    
  > - We can observe from the exhaustive method that if we increase the number of power level from 0 to 1 (all on to off/on), we can increase 35 Mbps. However, if we increase the number of power level from 1 to 2 (off/on to off/0.5on/on), we can only increase 2 Mbps.  

  >>>   | power level  | Capacity (Mbps) | 
  >>>   | :-: | :-: |
  >>>   |0 [all on]|223|
  >>>   |1 [on/off]|256|
  >>>   |2 [on/halfon/on]|258|


3. There is an upper bound when we use APC + RL

