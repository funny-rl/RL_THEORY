### hypothesis: Individual-Globa-Max(IGM)

$d$개의 에이전트에 대해, joint history $H$, 한 agent의 action space를 $A$, $A$의 확률 분포를 $P$로 정의하면 다음과 같이 기술할 수 있다. 
$$
\pi: H^d \rightarrow P(A^d), (1 \leq i, j \leq d)
$$
하지만 여기서 각 에이전트가 독립적으로 action을 선택한다면, 즉 두 $A_i$, $A_j$가 서로 독립이라고 가정하면 $P(A^d) = P(A)^d$로 표현 가능하다. 
각 agent가 본인의 관찰 정보만을 활용해 action을 선택한다는 가정하에, Joint action distribution을 d개의 individual distribution으로 factorize(분해)한다는 의미이다. 이것은 다음과 같이 표기될 수 있다.

### 의견

$\log$를 씌우면 곱을 합으로 분해 가능, 따라서 $\bold{Q}$를 최대화하는 문제라면 $\log$를 취한 뒤 덧셈으로 분해하고 사용한 $\log$를 지워도 상관없다. 따라서 아래와 같이 표현할 수 있다. 

$$
\bold{Q} = \sum^d_{i=1}Q_i
$$

이것이 IGM의 기원이다. 이후 다음과 같이 공식화된다. 

$$
\argmax_{\bold{u^d}}(\bold{Q}(\bold{\tau}, \bold{u^d})) = \left(\argmax_{A_i} Q_i(\tau_i, u_i)\right)^d_{i=1}
$$

이 가정은 VDN, QMIX 알고리즘을 통해 cooperative marl에서 대대적인 발전을 이루었고, 하나의 중요한 방법론으로 인정받았다. 
그러나 Non-monotonicity (비단조성)의 문제로 인해 한 에이전트의 희생을 요구하는 환경에서는 잘 학습되지 않는다는 단점이 있으며, 이것은 PG 방법론이 VD 방법론에 비해 좋은 결과를 가질수도 있게 되는 원인이 되었다. 


### 의견

위에서 활용한 가정 $\bold{Q} = \sum^d_{i=1}Q_i$은 각 agent의 action을 agent 별로 별도의 네트워크에 입력할 이론적 배경을 제공하는 것이지 단조성 제약에 대한 것이 아니다. 또한 이러한 IGM이 널리 사용되는 이유는 전역 최적해를 보장하기 때문이다. 각 에이전트의 행동 가치를 최대화함으로써 팀 게임의 행동 가치를 최대화할 수 있으므로 전역 최적해를 보장할 수 있다.


