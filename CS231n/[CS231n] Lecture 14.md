# Lecture 14

# 강화학습(Reinforcement Learning)

<img width="464" alt="Untitled" src="https://github.com/3umin/Study/assets/99728502/7d354924-4915-463a-843a-64ec2915edaa">

- 에이전트에게는 State ($S_t$)가 주어지고, Action ($a_t$)를 취해서 이에 따라 Reward ($r_t$)와 다음 상태 ($S_{t+1}$)가 주어짐
- 대표적인 사례
    - Cart-Pole Problem
        
        <img width="470" alt="Untitled 1" src="https://github.com/3umin/Study/assets/99728502/fb1b3dca-1640-4d58-acff-10aad0d083c7">

        - 움직이는 카트와 카트위에 달려있는 막대기의 균형을 유지하는 문제
        - State : 막대기의 각도, 막대기의 각속도, 카트의 위치, 카트의 수평속도
        - Action : 카트를 수평으로 미는 힘
        - Reward : 막대기가 제대로 서있으면 1점
    - Robot Locomotion
        
        <img width="503" alt="Untitled 2" src="https://github.com/3umin/Study/assets/99728502/24d11635-8316-471f-8bbb-8e3d686d6c5c">

        - 로봇이 앞으로 나아가도록 조종
        - State : 로봇의 모든 관절들의 각과 위치
        - Action : 관절에 적용되는 토크(힘)
        - Reward : 앞으로 나아갈 때 1점 + 똑바로 서있을 때 1점(휴머노이드 로봇만)
    - Atari Games
        - 단순한 게임의 게임 점수를 극대화
        - State : 진행화면 그대로
        - Action : Game Controls(상하좌우 등)
        - Score : 각각의 게임 스코어
    - 바둑
        - 게임을 이기는 것이 목표
        - State : 모든 돌의 위치
        - Action : 다음 돌을 어디에 둘지
        - Reward : 게임을 이기면 1점

## Markov Decision Process(MDP)

<img width="458" alt="Untitled 3" src="https://github.com/3umin/Study/assets/99728502/1fef6945-9f1d-448f-8575-146d5865349b">

- 강화학습을 수식화
    
    <img width="511" alt="Untitled 4" src="https://github.com/3umin/Study/assets/99728502/2f6564cd-3720-40f9-8277-433a3e456f77">

- 초기 상태인 t=0에서, 초기상태 분포인 $p(s_0)$에서 State $s_0$를 샘플링
    1. 에이전트가 action $a_t$를 결정
    2. Environment는 어떤 분포로부터 reward를 샘플링 (reward는 현재의 s와 a가 주어졌을 때의 보상)
    3. Environment는 다음 분포에서 $s_{t+1}$을 샘플링
    4. 에피소드가 종료될 때까지 이런식으로 보상과 다음상태를 받는 과정을 되풀이
- 여기서 policy $\pi$는 Action에 대한 State의 함수. 각 State에서 어떤 행동을 취할지를 나타내는 함수
- 우리의 목적은 Cumulative discounted reward ($\sum_{t>0} \gamma^t r_t$ )를 극대화하는 최적의 정책 $\pi ^ *$를 찾는 것

- 간단한 MDP 예시
    
    <img width="387" alt="Untitled 5" src="https://github.com/3umin/Study/assets/99728502/9b230709-dacf-44f8-ac4a-8544e2ceba99">

    - 격자 중 어디로든 이동할 수 있고, 상하좌우로 이동 가능
    - 한 번 움직일 때마다 음의 보상을 받으며, 목표는 회색으로 칠해진 “**종료 상태**”에 최소한의 경로로 이동
    - Random policy에서는 무작위로 방향을 결정
    - Optimal policy에서는 점점 더 종료상태에 가까워지도록 만드는 적절한 방향을 선택해서 행동을 취함
    - Optimal policy $\pi ^*$를 어떻게 찾을까?
    - 보상의 합에 대한 기댓값을 최대화시키면 된다
    - $\pi^* = argmax_{\pi} E[\sum_{t\ge0} \gamma^t r_t|\pi]$ with $s_0 \sim p(s_0), a_t \sim \pi(\cdot|s_t), s_{t+1}\sim p(\cdot|s_t, a_t)$

- 정의 : Vaue Function and Q-Value function
    - Policy를 따라 무언가를 수행하게 되면 결국 모든 에피소드마다 어떠한 “**경로**”를 얻게됨
    - 우리가 현재 속해있는 State가 얼마나 좋은 상태일까? → Value function
    - Value Function : 상태 s와 정책 $\pi$가 주어졌을 때 누적 보상의 기댓값
        - $V^\pi (s) = E[\sum_{t \ge 0} \gamma^t r_t|s_0 = s, \pi]$
    - 그렇다면 (State, Action)쌍이 얼마나 좋은지는 어떻게 알까? → Q-Value function
        - policy, action, state가 주어졌을 때 받을 수 있는 누적보상의 기댓값
        - $Q^\pi(s, a) = E[\sum_{t \ge 0} \gamma^t r_t|s_0 = s, a_0 = a, \pi]$
        - $Q^*(s, a) = max_{\pi} E[\sum_{t \ge 0} \gamma^t r_t|s_0=s, a_0=a, \pi]$
        
- Bellman equation
    - $Q^*(s, a) = E_{s'\sim \epsilon} [r + \gamma max_{a'} Q^*(s', a') |s, a]$
    - 어떤 (s, a)가 주어지던 간에, 현재 (s, a)에서 받을 수 있는 보상인 $r$과 에피소드가 종료되는 $s'$에서 받을 보상인 $r'$을 더한 값 → 현재 상태를 알고 있기 때문에, 최선이 되는 $s'$을 미리 정할 수 있다

- 어떻게 하면 최적의 정책을 찾을 수 있을까?
    - “Value iteration algorithm”
    - $Q_{i+1}(s, a) = E[r + \gamma max_{a'} Q_i (s', a')|s, a]$
    - $Q_i$는 i가 점점 커짐에 따라 $Q^*$에 수렴할 것임
    - 하지만 이 방법은 Scalable 하지 않다는 문제점
    - 만약 게임 픽셀이 State라면, State space가 아주 크며 기본적으로 전체 State space를 계산하는 것은 불가능
    - 해결책?
    - 우선 함수 Q(s, a)를 근사시킬 수 있음(Neural Net으로)

## Q-learning

<img width="422" alt="Untitled 6" src="https://github.com/3umin/Study/assets/99728502/ce15fe48-6296-47a6-a251-c3dbde99e509">

- action-value function을 근사(딥러닝 네트워크로)
- Neural Network로 근사시킨 Q-function을 학습시켜서 Bellman Equation의 에러를 최소화하면 됨
- $L_i(\theta_i) = E_{s, a \sim p(\cdot)} [(y_i - Q(s, a;\theta_i))^2],y _i = E_{s' \sim \epsilon}[r + \gamma max_{a'} Q(s', a';\theta_{i-1}|s, a]$
- Forward Pass에서는 손실함수를 계산하고, Backward Pass는 계산한 손실을 기반으로 파라미터 $\theta$를 업데이트

<img width="377" alt="Untitled 7" src="https://github.com/3umin/Study/assets/99728502/96683c84-3aaf-46fb-80b1-a57b944d10d2">

- Q-function에 사용한 네트워크 구조
    - 상태 S를 입력으로 받아 Q-value function을 직접 예측하는 것이 목표
    - 이러한 구조를 통해 한번의 forward pass만으로 현재 State에 해당하는 모든 함수에 대한 Q-value를 계산할 수 있음 → 효율적이다!
    - 앞서 정의한 Loss function을 통해 학습

- Experience Replay
    - Q-fuction 모델에선 하나의 배치에서 시간적으로 연속적인 샘플로 학습하면 안좋다
    - 연속적인 샘플들을 이용해서 학습시키면 모든 샘플들이 상관관계를 가져 비효율적
    - 또한 만약 현재 State에서 왼쪽으로 이동하는 것이 Reward를 극대화하는 행동이라면, 다음 샘플들도 전부 왼쪽에서 발생할 수 있는 것들로만 편향되는 문제 발생
    - 이 문제를 해결하는 방법 → “Experience Replay”
    - (State, Action, Reward, Next State)로 구성된 Replay memory를 이용
    - 즉 연속적인 샘플을 이용하지 않고 전이 테이블에서 임의로 샘플링된 샘플을 사용
    - Replay memory에서의 미니배치를 이용해 Q-network를 학습시키면 위 문제들을 해결할 수 있다

<img width="524" alt="Untitled 8" src="https://github.com/3umin/Study/assets/99728502/30bb28ea-80e8-4357-9473-528378afcc9c">

Experience Replay를 이용한 Deep Q-learning 알고리즘

- Q-learning의 문제점 → Q-function이 너무나도 복잡하다!
    - Q-learning에서는 모든 (state, action) 쌍을 학습해야 함
    - 이 조합의 경우가 많은 경우엔 학습이 어렵다
    - ex. 관절 하나하나가 많은 경우, 관절 하나하나가 어떻게 움직일지를 학습하는 것보단 ‘손을 오무린다’와 같은 정책을 학습
- 만약 Policy 자체를 학습한다면? → Policy gradients

### Policy Gradients

<img width="341" alt="Untitled 9" src="https://github.com/3umin/Study/assets/99728502/852d6673-6749-4f8a-87b0-c101e6ce5034">

- 이 상황에서 보상의 기댓값을 최대로 하는 정책 파라미터를 찾으면 됨 → policy parameter에 gradient ascent 수행!
    <img width="524" alt="Untitled 10" src="https://github.com/3umin/Study/assets/99728502/d581e4cf-345a-412f-a2b5-6a4e91c1f6d1">

    
- Reinforce algorithm에 적용하면, $J(\theta) = E_{r\sim p(\tau;\theta)} [r(\tau)] = \int_{\tau} r(\tau) p(\tau ;\theta)d\tau$
- $r(\tau)$  : 각 경로에 따른 보상
- 이 J 식을 미분하면 계산할 수 없기 때문에, 간단한 트릭을 사용해 우회해서 계산 (맨 아래 식)
- 기댓값에 대한 그레디언트를 계산하는 대신 그레디언트에 대한 기댓값을 계산 → Monte Carlo 샘플링
- 이 방법을 통해 어떤 경로 $\tau$에 대해서도 Gradient를 기반으로 $J(\theta)$를 추정해낼 수 있다
    
    <img width="491" alt="Untitled 11" src="https://github.com/3umin/Study/assets/99728502/342c90b6-4af1-408d-808b-cc8ccf8c598c">

- 구체적인 Q-Value를 몰라도 policy 자체의 gradient를 구해 최적의 policy를 찾을 수 있음
- Reward 측정기 J는 $\tau$가 높을 경우에 관찰된 행동의 확률을 높이고, $\tau$가 낮을 경우에는 관찰된 행동의 확률을 낮출 것임(즉 잘한 행동의 발생 확률은 높이고 못한 행동의 발생 확률은 낮출 것이다)
- 궤적(경로)이 좋을 경우에는 그 모든 action들이 좋았다고 평가받을 것처럼 보이지만 기댓값에 의해서, 그것은 평균화 되어버린다.
- 그러나 여기의 문제는 분산이 너무 높음
    - policy gradient의 분산이 크다 (안정적으로 학습되기 전까진 계속해서 좋고 나쁜 행동을 반복하기 때문)
    - 이는 Credit assignment가 아주 어렵기 때문 (Credit Assignment : Reward를 받았을 때 내가 취한 action 중 어떤 action이 좋은 action인지 등을 판단)
        - 일단 보상을 받았으면 해당 경로의 모든 행동들이 좋았다고 평가 받을텐데, 구체적으로 어떤 행동이 최선이었는지를 알아낼 수 있기보다는 모든 정보가 평균화 되어 버리는 바람에 어떤 행동이 특히 좋았는지 등을 알 수 없다
        - 어떤 행동이 좋았는지를 알지 못하기 때문에 좋은 행동을 계속하도록 학습하는것이 어려움
        - 결국 좋은 평가를 위해서는 충분한 샘플링밖에 답이 없다!

- Variance reduction 방법들
  
    <img width="458" alt="Untitled 12" src="https://github.com/3umin/Study/assets/99728502/90a62cc1-f674-4c2e-8c8b-2e413d671f84">

    
    1. 해당 State로부터 받을 미래의 보상만을 고려하여 어떤 Action을 취할 확률을 키워줌
        - Gradient를 계산하기 위해 reward를 계속 곱한 값을 더해주는데, 미래의 보상만 고려하니까 이 값이 작아짐 → Gradient도 작아짐 —> Variance 작아짐
    2. 지연된 Reward에 대해서 할인률을 추가하여 계산
    어떤 action이 좋은 action인지 아닌지를 가까운 곳에서 찾게됨
    
1. Baseline : Reward가 우리의 예상보다 좋은지 아닌지를 판단하는 것이 더 중요하다는 접근법
    - 그렇다면 Baseline을 어떻게 선택할 것인가?
    - 가장 단순한 Baseline : 지금까지 경험했던 보상들에 대해 Moving Average를 계산
    - 특정 State에 대해 기대할만한 값 → Value function.
    - 현재 Action이 얼마나 좋은 Action이였는지를 해당 상태에서의 Q-function과 value function의 차이를 통해 나타냄
    - Q와 V를 직접적으로 계산하진 않았는데, policy gradient와 Q-learning을 이용하여 학습할 순 있다
    - Actor - Critic 알고리즘
        - Actor : 어떤 행동을 취할지를 결정
        - Critic : 그 행동이 얼마나 좋았으며 어떤식으로 조절해나가야 하는지를 알려줌
        - 보상함수 : $A^\pi(s, a) = Q^\pi(s, a) - V^\pi(s)$

### Recurrent Attention Model(RAM)

<img width="495" alt="Untitled 13" src="https://github.com/3umin/Study/assets/99728502/66eba422-704e-41b4-89f8-63d77a1e4020">

- 이미지를 분류할 때 일련의 glimpses만 가지고 예측해야함
- 이미지의 어떤 부분을 볼지를 학습하는 모델
- 이 방법을 통해 연산을 줄일 수 있고, 중요한 부분만을 선별할 수 있기 때문에 실제로 성능이 더 올라갈 수도 있다
    
    <img width="522" alt="Untitled 14" src="https://github.com/3umin/Study/assets/99728502/5ed03d46-475c-4519-bb50-aee2cc07aac0">

- glimpses를 NN에 통과시키고 난 뒤, RNN을 이용해 지금까지 있었던 glimpses(state)를 전부 결합
- 이 행동 분포로부터 x, y를 샘플링 한 다음에 이 좌표를 이용해 다음 glimpse의 좌표를 구함

### Policy Gradients : AlphaGo
