에이전트 간의 수치적인 통신이 불가능한 MARL 환경을 가정

1. 한 에이전트의 과거부터 현재까지의 obs 기록을 이용해 과거에는 관측했지만 현재는 관측하지 못하는 object의 state를 추정하는 것을 목표로 함. 이것은 obs로 state를 추정하겠다고 하는 것과는 다름.

2. VD 분야에서 IGM을 무너뜨리고 협력할 수 있는 방안이 없는지 확인해보기.

적용 분야: 축구와 같은 스포츠 / 자율 주행 (타사 자동차와는 통신 불가, 주변에 있던 사람이 장애물을 통과해 갑자기 나타나는 경우)

환경 정의: 매번 관측되는 객체의 종류, 수가 달라지는 환경.

해결해야 할 문제: 
1. 학습에 따라 에이전트의 움직임도 달라지며 이에 따라 예측하는 동선, 상태도 달라져야 함. 이걸 어떻게 해결할 것인가?
2. 현재 11 vs 11은 안그래도 어려운데 그렇게 시야 제한과 같은 제약을 주어도 괜찮을까??

VD vs PG 
1. https://bair.berkeley.edu/blog/2022/07/10/pg-ar/ : PD와 PG의 한계

Keywords
1. belif state
2. DEC-POMDP(Decentralized POMDP, Dec-POMDP)
3. ego-centric learning

Definition
1. DEC-POMDP(Decentralized POMDP, Dec-POMDP) / NEXP-complete(비결정적 지수 시간 완전)

Algorithm

1. VDN

2. QMIX

3. QTRAN (IGM 가정 제거)

4. QPLEX (각 에이전트의 Q 함수를 base Q값과 Advantage로 분리)

Related Work

1. MA^2E: Addressing Partial Observability in Multi-Agent Reinforcement Learning with Masked Auto-Encoder ()
2. Latent Inference for Effective Multi-Agent Reinforcement Learning under Partial Observability ()
3. Enhancing Cooperative Multi-Agent Reinforcement Learning with State Modelling and Adversarial Exploration (communication)


Benchmark (Environment)

기존 GRF 실험의 경우, 시뮬레이션 도중에 GPU 가속이 안되기 때문에 실험 속도가 굉장히 더딤. 따라서 대체 환경을 찾을 필요성 있음. MARLadona의 경우 최신 issaclab을 기반으로 해서 그런지 요구하는 기본 사양이 높고, 지나치게 driver와 같은 기본 도구의 적합성을 엄격히 요구함. 현재 유력 후보는 VMAS를 활용한 football 환경, 코드 상으로는 11 vs 11이 가능해보임. self-play도 가능하지 않을까??

1. VMAS: A Vectorized Multi-Agent Simulator for Collective Robot Learning / https://arxiv.org/pdf/2207.03530 / https://www.youtube.com/watch?v=aaDRYfiesAY

2. Learning Complex Teamwork Tasks Using a Given Sub-task Decomposition / https://arxiv.org/pdf/2302.04944 (AAMAS 2024), Using VMAS for football training 

3. Cross Language Soccer Framework: An Open Source Framework for the RoboCup 2D Soccer Simulation (Robocup 2d python project) / https://github.com/CLSFramework / 진행중인 프로젝트