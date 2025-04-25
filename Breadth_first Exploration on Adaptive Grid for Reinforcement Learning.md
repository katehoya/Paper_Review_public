# Breadth-first Exploration on Adaptive Grid for Reinforcement Learning  

## Abstract  
 - Graph 기반 planner는 목적 결과가 있는 RL로써 주목을 받았다.  
   그 이유는 graph 기반 planner가 subgoal들 간의 신뢰도 있는 전이로 구성된 그래프를 만들고 이 edge를 활용하여 최단거리 알고리즘을 이용하기 때문이었다.  
   반면 불가능한 transition을 식별하고 피하는 것이 중요하지만 기존의 graph 기반 planner들은 이를 간과하고 있었고 불가능한 subgoal을 조사하느라 매우 많은 시도 횟수들이 낭비되었다.  
   이 문제를 해결하기 위해 이미 도달한 subgoals와 아예 불가능한 subgoals를 효율적으로 관리하고 goal space를 적응적으로 이산화하는 그래프 구성 방식을 제시한다.  
   이를 통해 지역적인 적응형 grid 정제를 기반으로 한 너비 우선 탐색(BFS)를 가능케 하며 이는 밀집된 그리드에서 조밀하게 하기 보단 넓은 그리드에서 넓게 조사하는 것을 우선시한다.  
   우리는 이론적 분석을 수행했고 BEAG가 fixed-goal 환경의 복잡한 환경에서 성공한다는 것을 실험적 근거를 통해 우리 방식의 효과를 증명했다.  

***
## Proposed Method    
![image](https://github.com/user-attachments/assets/f875ffd7-9288-49bb-ab16-079397a5c4f1)     
 - BEAG는 replay 버퍼에서 생성된 그래프에 의존하기 보다는 map 존체를 포함하는 grid를 이용한다.  
   replay 버퍼 기반의 그래프는 아직 탐사되지 않은 or 불가능한 subgoal을 포함할 수도 있다.  
   BEAG는 그래프에서 생성된 경로를 따라가며 유망한 subgoal을 탐핵한다. 이 과정에서 반복적으로 실패한 subgoal과 연결된 edge들은 제거된다.  

![image](https://github.com/user-attachments/assets/65c03ea6-a815-4bb1-969e-9b0d8c5f1d60)   
 - Bottleneck-maze와 같이 너비 우선 탐색에서 경로를 찾지 못하는 문제를 해결하기 위해 BEAG는 적응형 grid 정제를 이용한다.  
   1. 경로가 더 이상 생성될 수 없을 때까지 불가능한 subgoal들을 탐지한다.  
   2. 한 불가능한 node 근처에서 dense refinment를 실시한다.  
   3. 가능한 경로를 찾을 때까지 1,2번을 반복한다.  
***
## Theoritical Analysis  
![image](https://github.com/user-attachments/assets/da7c11d4-1453-4f41-ba47-8641a5b33a6d)   
 - (a) e-path가 존재하면 BEAG는 항상 가능한 경로를 만들어 낸다.  
   (b) grid 경로의 최대 길이는 ![image](https://github.com/user-attachments/assets/41cf057b-ca83-446c-8c46-bb0b1dfcc6ca)이다. (l은 e-path의 길이)  

***
## Main Results  
![image](https://github.com/user-attachments/assets/7c6376f0-807e-45cd-915c-c5a79652d423)  
 - 우리는 graph 기반의 RL 방법을 MuJuCo 환경의 set에서 평가했고, 이 환경은 reward가 희박하고, 장기적인 목표를 가지는 꽤 어려운 setup이었다.  
   특히 AntMaze의 목표는 2차원, Reacher3D의 목표는 3차원이라는 저믕ㄹ 주목할 수 있다.  

![image](https://github.com/user-attachments/assets/367ab215-4c35-4ee1-9082-a7d58147afa2)  
 - 평균 성공률을 실선으로, 표준 편차를 그림자 영역으로 표시했다.   
   BEAG와 다른 baseline 모두 고정된 초기값과 목표 설정으로 훈련되었다.  
   어떤 baseline은 값이 겹치거나 성공률이 0에 수렴해 특정 환경에서 보이지 않을 수도 있다.   
