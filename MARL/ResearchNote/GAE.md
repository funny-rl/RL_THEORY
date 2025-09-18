
## State / Action Value function

1. **Return**
$$
\begin{align}
G_t &= R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} + \cdots = \sum_{k=0}^{\infin}\gamma^k R_{t+k+1} \\ 
&= R_{t+1} + \gamma (R_{t+2} + \gamma R_{t+3} + \gamma^2 R_{t+4} + \cdots) \\
&= R_{t+1} + \gamma G_{t+1}
\end{align}
$$
Return은 $t$ 시점에 행동함으로써 받을 수 있는 보상을 $R_{t+1}$로 정의하기에 $\gamma$가 적용되지 않음. 만약 $t$ 시점에 행동함으로써 받을 수 있는 보상을 $R_{t}$로 정의한다고 해도 scale이 달라질 뿐 내면의 의미는 동일하다.


2. **state-value function** 
$$
\begin{align}
V^\pi(s) &= \mathbb{E}_\pi[G_t  | s_t = s] = \mathbb{E}_{\pi}\left[\sum^{\infin}_{k=0} \gamma^k r_{t+k+1} | s_t = s  \right] \\
&= \mathbb{E}_\pi[R_{t+1} + \gamma G_{t+1} | s_t = s] \\
&= \mathbb{E}_\pi[R_{t+1} + \gamma \mathbb{E}\left[G_{t+1} | s_{t+1} = s' \right] | s_t = s] \\
&= \mathbb{E}_\pi[R_{t+1} |  s_t = s] +  \gamma \mathbb{E}_\pi[\mathbb{E}\left[G_{t+1} | s_{t+1} = s' \right] | s_t = s] \\ \\
\mathbb{E}\left[ \mathbb{E}\left[X|Y\right] \right]&= \mathbb{E}[X] \\ \\

&= \mathbb{E}_\pi[R_{t+1} + \gamma \bold{V(s_{t+1})} | s_t = s] \\ 
&= R_s + \gamma \sum_{s' \in S}P_{ss'}V(s') \\
\end{align}
$$

Return이 하나의 trajectory를 따른다고 가정할 때, $G_{t+1}$은 $s_{t+1}$에서 가능한 모든 return의 기댓값이 되어야 한다. 따라서 위와 같이 전개할 수 있다.

2. **action-value function** 
$$
\begin{align}
V^\pi(s) &= \sum_{a \in A}\pi(a|s)Q^\pi(s, a) \\
Q^\pi(s, a) &= \mathbb{E}_\pi[G_t  | s_t = s, a_t = a] \\
&= \mathbb{E}_\pi[R_{t+1} + \gamma Q^\pi(G_{t+1} | s_{t+1}, a_{t+1})  | s_t = s, a_t = a] \\
&= R^a_s + \gamma \sum_{s \in S} P^a_{ss'} v_\pi(s') \\
V^\pi(s) &= \sum_{a \in A}\pi(a|s)\left[R^a_s + \gamma \sum_{s \in S} P^a_{ss'} v_\pi(s')\right] \\
Q^\pi(s, a) &= R^a_s + \gamma \sum_{s \in S} P^a_{ss'}\left[\sum_{a' \in A}\pi(a'|s')Q^\pi(s', a')\right]
\end{align}
$$

따라서 Q와 V는 서로를 활용해 표현할 수 있다. 

3. **Advantage function**

$$
\begin{align}
A(s_t, a_t) &= Q(s_t, a_t) - V(s_t)\\
A^{(1)}(s_t, a_t) &= R_{t+1} + \gamma V(s_{t+1}) - V(s_t) \\
A^{(2)}(s_t, a_t) &= R_{t+1} + \gamma R_{t+2} + \gamma^2 V(s_{t+2}) - V(s_t) \\
A^{(\infin)}(s_t, a_t) &= R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} + \gamma^3 R_{t+4} + \cdots - V(s_t) 
\end{align}
$$

$A^{(1)}, A^{(2)}$를 나타내는 각 수식은 결론적으로 동일하지만 내포하는 의미는 달라진다. $A^{(1)}$는 예측량이 상대적으로 많기 때문에 TD(high bias, low variance), $A^{(\infin)}$는 실제값을 기반으로 하기 때문에 MC에 가깝다(low bias, high variance). TD는 MC에 비해 가질 수 있는 root의 수가 적기 때문에 variance가 낮지만, 그로 인해 상대적으로 적은 상황만을 학습하게 되어 bias가 높다.

우리는 그럼 각 $A^{(n)}$ 중에서 뭘 사용해야 할까? $\rightarrow$ 한번 **모두** 사용해보자: **GAE**!

$$
\begin{align}
\delta^V_t &= R_t + \gamma V(s_{t+1}) - V(s_t) \\
A^{(1)} &= \delta^V_t \\
A^{(2)} &= R_{t+1} + \gamma\delta^V_{t+1} - V(s_t) + \gamma V(s_{t+1}) =  \delta^V_t + \gamma \delta^V_{t+1} \\
A^{(3)} &=  \delta^V_t + \gamma \delta^V_{t+1} + \gamma^2 \delta^V_{t+2} \\
A^{\text{GAE}}_t &= (1 - \lambda) (A^{(1)} + \lambda A^{(1)} + \lambda^2 A^{(2)} + \lambda^3 A^{(3)} + \cdots) \\
A^{\text{GAE}}_t &= (1 - \lambda) (\delta^V_t + \lambda (\delta^V_t + \gamma \delta^V_{t+1}) + \lambda^2 (\delta^V_t +  \gamma \delta^V_{t+1} + \gamma^2 \delta^V_{t+2})) \\
A^{\text{GAE}}_t &= (1 - \lambda) \left(\delta^V_t (1 + \lambda + \lambda^2 + \cdots) + \delta^V_{t+1} \gamma (\lambda + \lambda^2 + \cdots) + \cdots\right) \\
A^{\text{GAE}}_t &= (1 - \lambda) \left(\delta^V_t (\frac{1}{1-\lambda}) + \delta^V_{t+1} \gamma  (\frac{\lambda}{1-\lambda}) + \cdots\right) \\
A^{\text{GAE}}_t &= \delta^V_t + \lambda \gamma \delta^V_{t+1} +  \lambda^2 \gamma^2 \delta^V_{t+2} + \cdots\\
A^{\text{GAE}}_t &= \sum_{l = 0}^{\infin} \lambda^{l} \gamma^{l}  \delta^V_{t + l} \\
\end{align}
$$
일일이 다음 state에서의 모든 action을 수행해볼 수 없음으로 $Q - V$를 혼합해서 사용하는 식으로 $A^{GAE}$를 사용하는 것은 불가능 