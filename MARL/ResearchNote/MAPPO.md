## MAPPO

**1. Actor**
$$
r^i_{\theta, j} = \frac{\pi_{\theta}(a^{i}_j | o^i_j)}{\pi_{\theta_{old}}(a^{i}_j | o^i_j)} \\
A^i_{j,t} = \sum_{l=0}^h\left( \gamma\lambda \right)^l \left(\hat{R}_{j,(t+1+l)} + V_\phi(s^i_{j,(t+1+l)} - V_\phi(s^i_{j,(t+l)})\right)^i \\ \

L(\theta) = \left[\frac{1}{Bn} \sum^B_{j=1}\sum^n_{i=1}\min\left(r^i_{\theta, j}  A^i_j, \text{clip}(r^i_{\theta, j} , 1-\epsilon , 1+\epsilon)A^i_j\right)\right] + \frac{\sigma}{Bn} \sum^B_{j=1}\sum^n_{i=1} S\left[\pi_\theta(o^i_j)\right]
$$

**2. Critic**
$$
L(\phi) = \frac{1}{Bn} \sum^B_{j=1}\sum^n_{i=1}\max\left[\left(V_\phi(s^i_j)-\hat{R}_j\right)^2, \left(\text{clip}\left(V_\phi(s^i_j), V_{\phi_{\text{old}}}(s^i_j) - \epsilon, V_{\phi_{\text{old}}}(s^i_j) + \epsilon \right) -\hat{R}_j \right)^2 \right]
$$


MAPPO의 경우, 에이전트마다 계산하는 Advantage의 기준값이 동일하기 때문에, Actor의 agent network에서 모든 에이전트가 동일한 advantage value를 기반으로 학습하게 되어 sample efficiency가 극히 낮아진다. 특히 이 방식은 Lazy Agent 문제를 가속화하며, 이 문제는 다수의 에이전트가 각자 sub goal을 가지는 cooperative marl 환경에서는 치명적으로 작용할 수 있다.


# New Algorithm

$$
\begin{align}
Q^i_{j,t} &= R^i_{j,t+1} + \gamma V_{j,t+1}\\
A^i_{j,t} &= Q^i_{j,t} - V_{j,t} \\
 &= \left[R^i_{j,t+1} + V_{j,t+1}\right] - V_{j,t})\\

L^{joint}_{j, t} &= \left(\left[R^{\text{joint}}_{j,t+1} + \gamma V_{j,t+1}(s^i_{j,t+1})\right] -  Q^{\text{joint}}_{j,t}\right)^2 \\
&= \left(\left[R^{\text{joint}}_{j,t+1} + \gamma G^{\text{joint}}_{j, t+1}\right] -  Q^{\text{joint}}_{j,t}\right)^2  + (G^{\text{joint}}_{j, t} - V_{j,t})^2\\ 
L^i_{j, t} &= \left( \left[R^{i}_{j,t+1} + \gamma Q^i_{j,t+1}\right] - Q^i_{j,t} \right)^2  \\

\end{align}
$$
$L^{joint}_{j, t}$ 의 수식은 Critic이 Joint Q와 V를 동시에 근사하도록 의도하는 것에 착안한다. $L^i_{j, t}$ 의 수식은 에이전트마다의 $R^i_{j, t+1}$를 직접적으로 계산하는데 활용하며, $L^{joint}_{j, t}$의 수식에 활용할수도 있다. 

$$
\begin{align}
\frac{\partial L^{joint}}{\partial \phi^{\text{ind}}} = \frac{\partial L^{joint}}{\partial Q^{\text{joint}}} \sum_{i \in I} \frac{\partial Q^{\text{joint}}}{\partial Q^i} \frac{\partial Q^i}{\partial \phi^{\text{ind}}} \\


\end{align}
$$

$\phi^{\text{ind}}$는 critic에서 V와 $Q^i$를 출력하는 부분의 파라미터, $\phi$는 $Q^\text{joint}$를 계산하는 critic 전체 파라미터. 이때 IGM (individual global max)는 필요없어 보이지만 의견이 필요함. 



하지만 각 에이전트가 서로 다른 보상을 받을수만 있다면, 에이전트의 수만큼 sample efficiency가 높아질 수 있고, 네트워크가 개별 에이전트의 행동을 어떻게 평가하는지 수치적으로 추적할 수 있다.




