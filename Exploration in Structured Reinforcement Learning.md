# Exploration in Structured Reinforcement Learning  
***
## 1. Introduction  
 - Real World RL은 보통 state와 action space가 매우 큰 dynamic한 시스템과 연관이 있는데, 때문에 효율적인 알고리즘을 설계하기가 어렵다.  
   이런 어려움은 잘 알려진 regret의 이론적 하한선으로 설명된다.  
   Regret은 optimal policy의 누적 reward와 우리가 고려하는 알고리즘(policy)와의 누적 reward를 비교한다.  
   이를 통해 system dynamics와 reward를 익히기 위한 비최적의 (s,a)쌍을 선택함으로써 얻는 손실을 정량화한다.  
   undiscounted reward를 사용하는 RL문제에서 regret의 하한선은 S*A*logT 또는 sqrt(SAT)로 된다. S : state space, A : action space, T : time horizon  
   그래서 큰 state,action space에서는 system dynamics 내의 가능한 구조를 많이 확인하고 가 보는 게 필수적이다. 그리하여 exploration phase를 최소화하고 regret을 합리적인 value로 줄일 수 있다.  
 - 근대의 RL 알고리즘들은 구조적인 특성을 모델 parameter나 Q-function에 부여한다.  
   그러나 이런 연구들에도 불구하고 structured RL problem에 대한 이해는 여전히 부족하다.  

 - 이 논문에서는 structured RL 문제를 한정된 state, action space로 다룬다.  
   우리는 우선 어떤 임의의 구조에서 어떤 알고리즘을 사용하더라도 만족하는 문제에 특수화된 regret의 하한값을 설정한다.  
   이 하한선들은 exploration과 exploitation을 최적으로 균형을 맞추는 알고리즘을 고안하는 데 유용하다.(즉 regret의 이론적인 하한선이라는 목표를 잡는 것)  
   비슷한 접근이 stochastic bandit prpoblem에서도 성공했으며 arm의 평균 reward가 unimodality, Lipschitz continuity, 기타 general propertiy와 같은 구조적인 특성을 가졌다.  
 - 이 결과를 RL 문제로 확장하는 것은 시도되지 않았으며, 우리가 알아본 바로는 structured RL problem에서 문제에 특수화된 regret의 하한선을 제공하는 첫 번째 논문이다.  
   이 논문에서 나온 결과들은 undiscounted reward를 갖는 ergodic RL문제와 연관이 있지만 그들은 discounted reward 문제로도 쉽게 일반화 가능하다.  

### Our Contributions are as follows :   
1. ergodic structured RL 문제에서, 문제에 특수화된 regret의 하한선을 유도했다.  
   이 하한선은 structured specific한 어떤 구조에도 유효하며 system dynamics와 reward function를 모르는 상황에도 유효하다.  
2. 우리는 unstructured MDP에서의 하한선을 분석하고 그 크기는 ![image](https://github.com/user-attachments/assets/00461b44-296a-473f-b496-f133e8a014b4)이하였다. (H는 편향 함수의 span(최대와 최소의 차), d_min은 비최적 (s,a)쌍의 최소 gap)  
   이 결과는 regret의 하한값을 유도한 선행 연구를 reward function을 모르는 상황으로 확장한 것이다.  
3. 우리는 regret의 하한값을 Lipshitz MDP의 경우로도 확장했다.  
   이것의 상한값은 ![image](https://github.com/user-attachments/assets/51cdc561-c64d-4af7-9dd8-9b1e40fc23bc)이다. (S_lip,A_lip은 transition prob와 reward function의 Lipshitz특성에 의존한다)  
   이 식은 만약 H와 d_min이 s,a space의 크기와 비례해 커지지 않는다면 regret이 time horizon에 log적으로 증가하고 S,A와는 무관하다는 것을 의미한다.  
4. DEL 알고리즘을 제안한다.  
   DEL으로 regret의 하한선을 어떤 structured MDP에서도 얻을 수 있다.  
   DEL은 각 round마다 regret 하한선을 얻는 것과 비슷한 최적화 문제를 풀어야 하므로 구현이 복잡하다.  
   그래서 DEL을 단순화한 버전을 만들었으며 이를 통해 unstructured, Lipschitz MDP에서의 regret을 scaling할 수 있다.  
   또한 DEL은 기존 OLP방법과 달리 structure이 없는 상황에서 action index를 계산할 필요갸 없다. 그리고 reward function을 몰라도 이와 비슷한 regret을 보장한다.  
   DEL은 각 step에서 추정된 MDP의 optimal policy와 간단한 선형 문제만 계산하면 된다.  
5. 부록에서 제시한 사전적 수치 실험 결과는 이 이론적 주장을 뒷받침한다.  
   특히 우리는 DEL의 regret이 S,A에 비례하지 않는 그리고 구조를 exploit하지 않는 알고리즘보다 훨씬 뛰어난 Lipschitz MDP의 예시를 제공한다.  
***
## 2. Related Work  
 - regret의 하한선은 unstructured ergodic RL problem에에서 많이 연구가 되었다.  
   과거 논문에서는 우리와 비슷하게 문제에 특수적인하한선을 제공했지만, 이는 reward 함수를 알고 있는 경우에만 유효했다.   
   Minimax regret 하한선에 대한 연구도 이루어졌다. 최악의 경우 regret은 sqrt(DSAT)로 scaling되어야 한다. (D는 mdp의 지름)   
   이런 연구에도 불구하고 unstructured RL problem에서의 regret의 하한선은 여러 의미 있는 논의들로 인해 여전히 관심을 얻고 있다.  
   우리가 알기로는 이 논문이 structured RL problem 경우에서 regret 하한선을 유도한 첫 번째 시도이다.  
   우리의 하한선은 시간 지평선 T에 점근적이지만, 이것들을 bandit problem에서 하한선을 구하기 위해 사용하는 것과 비슷한 기술들을 이용하여 유한한 시간 지평선으로 확장하길 희망한다.  
   이 기술들은 문제 특이적이고 minimax하한선을 모두 다룬다. 그리고 structured RL problem에서의 minimax 하한선을 유도하는 데도 이용된다.  
   그러나 우리는 minimax 하한선을 예측하는 것이 구조를 이용함으로써 얻는 regret gain에 관해 informative하지 않다고 생각한다.  
   왜냐면 unstructured RL에서 최악의 regret으로 이끄는 MDP가 많은 구조를 따르고 있기 때문이다.  
     
 - Ergodic unstructured RL problem을 위해 개발된 다양한 알고리즘이 개발되어 왔다.  
   이러한 할고리즘들은 regret이 증가하는 양상에 따라 두 부류로 나눌 수 있다. logT로 증가하는 경우, sqrt(T)로 증가하는 경우  
   unstructured에서 점근적으로 최적의 알고리즘이 개발되기는 했지만 우리 알고리즘보다 더 복잡하다.  
   몇몇 알고리즘은 regret이 logT로 증가하는 finite-time도 보장한다.  

 - 기존에 알고있는 구조를 이용하기 위해 고안된 알고리즘은 continuous한 state, action space에서 더 적용하기 좋다.  
   보통 상태 전이 확률과 reward function이 state와 action에 대해 부드럽고 Lipschitz 연속이라고 가정한다.  
   그럼 regret은 T의 거듭제곱에 비례하여 증가하며 1차원 state space에선 T^2/3이다.  
   상태 전이 확률이 기존에 알려진 어떤 함수에 속한다는 접근은 과거 연구에서 제안된 바 있다.  
   이 뎐구에서 유도된 Regret의 상한선은 Kolmogorov와 eluder 차원이라고 불리는 것에 의존하며 이는 선택된 함수 class에 따라 결정된다.  
   구조를 이용하는 학습 알고리즘을 디자인하는 우리의 접근은 모든 선행연구들과 다르다.  
   이는 우리 연구가 비최적(s,a)쌍의 문제 특이적인 최소 exploration rate을 계산하는 것을 목표하기 때문이다.  

***   
## 3. Models and Objectives  
 - 이 연구는 각각 S,A의 카디날리티를 가진 유한한 state 공간 S, action 공간 A의 MDP pi = (p_pi, q_pi) 를 가정한다.  
   p_pi와 q_pi는 pi의 transition, reward 커널이다. state x에서 action a를 하면 state y로 p_pi(y|x,a)의 확률로 이동하고 reward는 q_pi('|x,a)로부터 평균 r_pi(x,a)를 받는다.  
   reward는 일반성을 잃지 않고 [0,1]의 구간에 존재한다.  
   또한 모든 (x,a)쌍에 대해 q('|x,a)는 [0,1]에서 연속이다.  

 - Z_t(X_t, A_t, R_t)는 step t에서의 state, action, reward를 의미한다.    
   pi는 policy를 의미하고 history H_t^pi는 pi 하에서의 (Z1, Z1... Z_t-1, Xt)를 의미한다.  
   대문자 pi는 이 모든 policy들의 합을 의미한다.  

 - Structured MDPs  
   MDP pi는 초기에 모르는 상태이다. 그러나 pi가 사전에 알고 있는 구조인 MDP로 인코딩될 수 있는 집합 omega에 속한다고 가정한다.  
   그래서 이 집합의 지식을 활용하면 효율적인 policy를 고안할 수 있다.  
   이 논문에서 도출된 결과들은 모든 구조 하에서 유효하지만 아래 두 케이스에 좀 더 집중한다.  
   1. Unstructured MDPs :![image](https://github.com/user-attachments/assets/843c1b97-ada9-4322-8ebd-0523ad10d23e)  
   2. Lipschits MDPs : ![image](https://github.com/user-attachments/assets/bae82a0c-46a2-43df-971e-d0abe25d9cdb)  

 - The Learning problem  
   policy pi, step T까지의 누적 reward 기댓값은 ![image](https://github.com/user-attachments/assets/8bc474ff-9a22-4ea9-96f9-9016bae29534)이다.  
   정리해 보자면 system은 state x에서 시작하고, omega집합에 속하고 초기에 알려지지 않은 MDP pi에 따라 나아간다.  
   objective는 ![image](https://github.com/user-attachments/assets/4c216666-fa7b-470e-9149-4bab2a1e25a5)를 최대화하고, regret![image](https://github.com/user-attachments/assets/abe53f7a-7111-4d60-a7bd-a141b0a1a5cf)을 최소화하는 policy pi를 찾는 것이다.  
   ![image](https://github.com/user-attachments/assets/d52d654f-ab60-4a21-a558-64d390e13e4f)  
    
***
## 6. Extensions and Future Work    
 - 이 논문에서 연구한 접근을 structured discounted RL로 확장하는 것은 가치가 있다.(그 문제에서는 알고리즘의 regret을 어떻게 정의할 것인가에 대한 이상적인 방법이 없긴 하다)  
   또한 우리의 방식이 모든 종류의 구조를 포함하기 때문에 Lipschitz 연속성에 해당하는 것보다 더 강한 구조에 대해 regret의 하한선을 구체화 할 필요가 있다. 예를 들어 reward가 unimodality나 convexity의 성질을 가지는 경우.  
   그런 구조에선 regret의 개선이 더욱 명확할 것이다.  
   또 다른 흥미로운 방향은 본 논문의 결과를 communicating MDP의 경우로 일반화하는 것이다.  
   이렇게 되면 deterministic system dynamics이지만 probabilistic reward를 고려하는 경우까지도 다룰 수 있다.  
***
