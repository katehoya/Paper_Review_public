## DIP-QL_A_Novel_Reinforcement_Learning_Method_for_Constrained_Industrial_Systems   
***
# Introduction  
산업계에서 순차적 제어 문제(sequential control problem)을 풀기 위해 많은 시도가 있었지만 변수의 미세 조정과 최적화 문제 때문에 쉽지가 않았다.   
최근 RL을 이용해 이 문제들을 풀기 시작했지만 실제 현장에서 사용하기는 많은 제약이 있었다.  
전형적인 RL method를 적용하기엔 한계가 있었기에 제한적인 control을 해야만 했다.   

i) Constrained MDP-based RL  
 - policy - dependent 특징들을 제한함으로써 policy update를 하는 방법.
 - 그러나 이 방법은 제약을 기댓값의 형태로 만족시키는 방식으로 control policy를 사용했기 때문에 모든 state의 모든 제약을 만족시킬 순 없었다.
 - 그래서 safety control problem등에는 부적합했고, *near-constraint satisfaction*으로 불린다.  

ii) RL agent가 action을 선택할 때 additional optimization layer(OptLayer)를 이용한 방법  
 - 만약 agent가 선택한 방식이 제약조건을 위배한다고 판단이 들면 OptLayer가 해당 action을 제약을 만족하는 공간으로 projeciton(투영)한다.
 - *exact-constraint satisfaction*이라고 불리며 safety control problem에도 사용이 가능하다.

Safety 제어문제가 필요한 로봇 제어문제에선, 제약 제어문제와 RL-based 접근이 uncertain dynamics와 측정할 수 없는 strict-feedback system의 측면에서 이루어졌다.  
그러나 제약이 있는 multiple-robot control에선 RL 방식으로 제어문제를 적용하기 힘들다.  
***이를 해결하기 위해 댜중 에이전트 시스템에서의 coordinated policy를 최적화하는 프레임워크를 고안했다.***   

Microgrid란 여러 제약이 있는 특정한 시스템 내에서의 전력 수요와 공급의 밸런스를 만족시키기 위해 분산된 에너지 자원(DERs)을 통합하는 시스템이다.   
병원과 같이 전력 수요가 중요한 곳에서는 이 신뢰도가 높아야 하기 때문에 *exact-constraint satisfaciton*이 필요하다.  
뿐만 아니라 몇몇 DERs는 ramp-rate control이나 capacity limit과 같은 제약도 만족시켜야 한다.  
위와 같은 여러 제약들을 어기는 것은 엄청난 비용이 발생하거나 운영이 불가능해지는 등의 심각한 문제이기 떄문에 microgrid control은 hard constraint가 필요하고, exact-constraint satisfaction 문제로 여겨져 왔다.  

몇면 선행 연구는 최적 제어 plan을 만들기 위해 stochastic dynamic programming과 같은 최적화 기법을 이용했다.  
그러나 대부분의 것들은 심한 계산 부담이 있었다. 그래서 large-scale microgrid system의 장기적인 측면에서는 부적절했을 뿐만 아니라 system dynamics의 stochastic feature를 충분히 포착하지도 못했다.  
이를 해결하기 위한 방법이 고안되었는데  
초기의 몇몇 연구는 system constraints를 고려하지 않고 microgrid 제어 문제를 풀었다.  

몇몇 연구는 system constraints를 고려하여 migrogrid control problem을 연구했다.  
1) near-constraint satisfaction으로 문제를 푼 유형.
 - reward function에 penalty term을 logarithmic barrier method로 추가.
 - 또한 microgrid의 DERs를 관리하는 multiple agents를 제어하기 위한 cooperative RL을 제안했다. 이는 차원의 저주를 피할 수 있었다.

2) exact-constraint satisfaction로 문제를 푼 유형.
 - 주로 RL을 이용했다.
 - Zhang.은 DERs를 관리하는 agent간 불완전한 정보를 갖고 있는 분산 microgrid system을 해결하기 위해 bi-RL 프레임워크를 제시했다.
 - 그들의 상위 연구에서 어떤 연구자는 규칙기반 매핑을 RL agent의 의사결정과 결합하여 microgrid의 전력 균형 방정식을 정확하게 만족시켰다.
 - 뿐만 아니라 진화 알고리즘과 RL방법을 결합하는 방식으로도 또 다른 문제를 해결했다.

이전의 연구들과 비슷하게, 우리가 증명하고자 하는 문제도 *exact-constraint satisfaction*형식이다.  

Microgrid control system의 분야에 국한되지 않고, exact-constraint satisfaction에 대한 연구가 많이 이루어지고 있다.  
 - [20]-[22]의 연구는 원래 선택된, 실행 불가능한 영역에 있는 action을 가장 가까운 실행 가능한 영역으로 대체하는 방법을 제시했다.
 - 이 때 그들은 constraint action space를 처리하기 위해 L2-projection 문제를 해결할 수 있는 층을 하나 더 쌀았다.   

 - [20]에선 로봇 팔의 제어를 보장하기 위해 quadratic programming(QP)를 해결할 OptLayer를 policy network의 상단에 추가했다.
 - 이후 로봇팔은 OptLayer의 solution을 이행하면서도 policy network 출력과의 차이도 최소화하면서 제약도 만족했다.
 - 또한 OptLayer의 과정 중, 실행 불가능한 action이 실행 가능한 영역으로부터 얼마나 떨어졌는지를 penalty cost라고 정의하여 해당 action의 가치를 낮추게 했다.
 - 비슷하게, [21]은 unconstrained policy와의 차이를 최소화하는 action을 선택하는 연구를 했다.
 - 복잡한 비선형 제약을 선형 제약으로 근사하기도 했다.
 - 
