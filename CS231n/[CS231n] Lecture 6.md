# Lecture 6

### Mini-batch SGD

1. data의 batch에서 샘플을 추출
2. Forward pass를 수행하고, loss를 계산
3. Backprop 하며 Gradient를 계산
4. Gradient 값을 이용해 파라미터를 업데이트

# Activation Function(활성화 함수)

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled.png)

- 데이터 입력이 들어오면 가중치와 곱한 뒤 활성화 함수를 거침

### Sigmoid

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%201.png)

- $\sigma (x) = 1 / (1+e^{-x})$
- 각 입력을 받아서 그 입력을 [0, 1] 범위의 값이 되도록 함
- 0 근처 구간은 선형함수 같이 생김
- Sigmoid가 뉴런의 “firing rate”를 saturation 시키는 것으로 해석할 수 있음
- Sigmoid의 문제점
    1. Saturation 된 뉴런이 gradient를 없앰
        - x = -10, 10 인 경우에 gradient는 거의 0에 가까운 값이 계산됨 → gradient가 죽어버려 밑으로 계속 0이 전달
    2. Sigmoid의 출력이 zero-centered 하지 않음
        - Sigmoid의 입력(X)가 모두 양수일 때, W에 대한 gradient를 계산하면 local gradient가 그냥 X가 됨
        - 모든 X가 양수이기 때문에, gradient의 부호는 그저 위에서 내려온 gradient의 부호와 같아짐
        - 결과적으로 W는 모두 같은 방향으로만 움직임(파라미터를 업데이트 할 때 다 같이 증가하거나 감소하는 경우일 뿐) 
        → 이러한 gradient 업데이트는 매우 비효율적
    3. exp( ) 연산이 계산 비용이 큼(중요한 문제는 아님)

### Tanh

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%202.png)

- Sigmoid와 유사하지만, 범위가 [-1, 1]
- zero-centered 활성화 함수
- 여전히 gradient가 평평해지는 구간이 있어 gradient killing 문제가 남아있음

### ReLU

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%203.png)

- $f(x) = max(0, x)$
- 양의 값에서는 saturate 되지 않기 때문에 gradient를 죽이지 않음
- 계산 효율이 아주 뛰어남
- 생물학적 타당성이 뛰어남(신경과학적인 실험)
- ImageNet 2012에서 우승한 AlexNet이 처음 ReLU를 사용하기 시작!
- 문제점
    1. zero-centered 하지 않기 때문에 Sigmoid에서의 문제점(가중치가 한 번에 같은 방향으로 업데이트)이 존재
    2. 음수에서는 saturated 되어 gradient를 killing
- 2번째 문제점 때문에, Dead ReLU 문제가 발생할 수 있음
    
    ![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%204.png)
    
    - 평면의 절반만 activate 되고, dead ReLU 층은 전혀 activate 되지 않아 update도 되지 않음
    - 발생하는 이유
        - 초기화를 잘못한 경우
        - Learning Rate가 너무 클 경우
    - Dead ReLU 문제 때문에 bias를 초기화 할 때 아주 작은 양수(0.01)로 초기화 하기도 하지만, 실제로 효과가 있는지는 검증되지 않았고 많은 사람들은 0으로 초기화

### Leaky ReLU

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%205.png)

- $f(x) = max(0.01x, x)$
- 음수에서 더 이상 0이 아니게 되어 ReLU의 gradient Killing 문제를 해소
- 여전히 계산은 효율적
- dead ReLU 현상도 없음

### PReLU(Parametric Rectifier)

- $f(x) = max(\alpha x, x)$
- 기울기가 alpha라는 파라미터에 의해 결정.
- alpha를 딱 정해놓는 것이 아니라, backprop으로 학습시키는 파라미터로 설정
- 활성화함수가 더 유연해짐

### ELU

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%206.png)

- $f(x) = x \ \ if \ \ x > 0,\ \ f(x) = \alpha (exp(x) -1) \\ if \\ x \le 0$
- zero-mean에 가까운 출력값
- negative에서 기울기를 가지는 대신에 또 다시 saturation이 됨
- 이런 saturation이 좀 더 잡음에 강인할 수 있음
- ReLU와 Leaky ReLU의 중간(Leaky ReLU처럼 zero-centered이면서, ReLU처럼 saturation도 있음)

### Maxout Neuron

- 입력을 받아들이는 특정한 기본 형식을 미리 정의하지 않음
- $f(x) = max(w_1^Tx + b_1 , w_2^Tx + b_2)$
- 선형이기 때문에 saturation 되지 않으며, gradient가 죽지 않음
- 하지만 뉴런 당 파라미터의 수가 두 배가 됨

### In practice

- ReLU가 가장 표준. 하지만 Learning rate를 설정할 때 주의
- Leaky ReLU / Maxout / ELU는 아직 실험단계(17년 기준)
- tanh는 시도해볼만 하지만 기대하진 말 것
- sigmoid는 사용하지 말 것
- Saturation이 너무 많으면 안좋은 것은 맞지만, 그렇다고 너무 Saturation이 없이 선형인 부분에만 존재하는 것도 문제 → BatchNormalization 및 스케일링을 하는 이유

## Preprocessing(데이터 전처리)

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%207.png)

- zero-centered 해야하는 이유: 데이터의 부호가 항상 동일하면 모든 뉴런이 동일한 부호의 gradient update를 하기 때문
- normalization 하는 이유 : 모든 차원이 동일한 범위에 있게 해서 동등한 기여를 하게 함
- 이미지에서는 각 차원 간에 스케일이 어느정도 맞추어져 있기 때문에, normalization을 잘해주지 않아도 상관없다!

- 머신러닝에서는 PCA, Whitening 등의 기법도 활용하지만, 이미지에선 단순히 zero-mean 정도만 주로 사용
- 일반적으로 이미지를 다룰 때는 굳이 입력을 더 낮은 차원으로 project 하지 않음
- zero-mean은 sigmoid의 첫 층에는 문제를 예방할 수 있지만, 그 이후의 층에는 sigmoid의 문제가 여전히 발생

## Weight Initialization(가중치 초기화)

- 만약 W = 0으로 가중치를 초기화한다면, 모두 똑같은 연산을 수행하는 문제가 발생할 수 있음(gradient도 모두 같고, 출력도 모두 같기 때문에 모든 가중치가 같은 값으로 업데이트)

1. 임의의 작은 값으로 초기화
    
    ```python
    W = 0.01 * np.random.randn(D, H)
    ```
    
    ![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%208.png)
    
    - 작은 네트워크라면 괜찮지만, 깊은 네트워크에선 문제가 생길 수 있음
    - 깊은 네트워크에서 W를 곱하면 곱할수록 W가 너무 작은 값들이라 0으로 수렴하게 됨
    - Backprop에서도 W를 계속 곱하기 때문에, upstream gradient도 0으로 수렴하게 됨
2. 가중치를 좀 더 큰 값으로 초기화를 한다면?
    
    ![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%209.png)
    
    - 가중치가 큰 값을 가지므로 tanh의 출력은 항상 saturated 될 것임.

→ 가중치가 너무 작으면 Gradient가 사라져버리고, 너무 크면 Saturation 되는 문제가 발생한다.

### Xavier initialization

```python
W = np.random.randn(fan_in, fan_out) / np.sqrt(fan_in)
```

![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%2010.png)

- Standard Gaussian으로 뽑은 값을 입력의 수로 스케일링해줌
- 입/출력의 분산을 맞추어지는 기능
- 입력의 수가 작으면 더 작은 값으로 나누어 큰 값을 얻고, 반대의 경우엔 작은 값을 얻으며 값을 조정
- 자비에 초기화 기법은 선형 활성화 함수를 사용하는 것을 전제.
- 만약 ReLU를 사용한다면 출력의 절반을 죽임(절반은 매번 0이 됨) → 출력의 분산을 반토막 내버림
    
    ![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%2011.png)
    
    - 이 문제를 해결하기 위해선 추가적으로 2를 더 나누어줌
        
        ```python
        W = np.random.randn(fan_in, fan_out) / np.sqrt(fan_in/2)
        ```
        
        ![Untitled](Lecture%206%20478b5554ad39489095a47461ad90d841/Untitled%2012.png)
        

## Batch Normalization

- Batch Normalization의 핵심 아이디어는 레이어의 출력이 가우시안 분포를 따르도록 만들기
- $\hat{x^{(k)}} = {x^{(k)} - E[x^{(k)}] \over \sqrt{Var[x^{(k)}]}}$
- 가중치를 잘 초기화 시키는 것 대신에 학습할 때 마다 각 레이어에 Normalization을 해주어 모든 레이어가 Unit Gaussian이 되도록 해줌
- 입력 데이터의 각 차원별로 평과 분산을 계산한 다음에, 한 Batch 내에 이를 전부 계산해서 Normalize 해줌
- 아주 유용하며 실제로도 자주 사용되는 기법

- tanh layer의 input에 gaussian 데이터가 들어가기를 원하는 것인가?
    
    → Normalization이 하는 일은 입력이 tanh의 linear한 영역에만 존재하도록 강제하기 때문에, saturation이 전혀 일어나지 않게 됨
    
- Batch Normalization 이후에, scaling 연산을 추가(원한다면)
    
    $y^{(k)} = \gamma^{(k)} \hat{x^{(k)}} + \beta^{(k)}$, $\gamma^{(k)} = \sqrt{Var[x^{(k)}]}$, $\beta^{(k)} = E[x^{(k)}]$
    
    - 감마로는 스케일링의 효과를, 베타로는 이동의 효과 (normalized 된 값들을 다시 원상복구 할 수 있도록 함)
    - 이러한 scaling을 통해 네트워크가 데이터를 tanh 함수에 얼마나 saturation 시킬지를 학습하기 때문에 유연성을 얻을 수 있음

## Babysitting the Learning Process

- Step 1 : Preprocess data
- Step 2 : Choose the architecture
- Step 3 : Double check that the loss is reasonable
    - Loss가 제대로 되었는지 확인하는 방법은 데이터의 일부만 우선 학습시켜 보기
    - 데이터가 적으니 당연히 Overfitting 되어 Loss가 매우 작겠지만, Loss가 Epoch마다 잘 내려가는지를 확인
- sanity check가 끝났다면 전체 데이터셋을 사용하고, regularization을 일부 사용하면서 learning rate를 조정
- learning rate가 지나치게 작다면 gradient 업데이트가 충분히 일어나지 않게되어 Loss 업데이트가 잘 일어나지 않음
- learning rate가 너무 크다면 cost가 발산(explode)할 수 있음
- 보통 learning rate는 1e-3 ~ 1e-5 사이의 값을 사용. cross-validation을 통해 learning rate를 결정

## Hyperparameter Optimization

- Cross-validation
    - training set으로 학습시키고 validation set으로 검증하는 방법
    - Coarse Stage : 넓은 범위에서 값을 골라냄
    - Fine Stage : 좀 더 좁은 범위를 설정하고 학습을 더 길게 시켜보며 결정

- Grid Search
    - 하이퍼파라미터를 고정된 값과 간격으로 샘플링
- 실제로는 grid search보다는 random search가 더 좋다.
- 하이퍼파라미터 종류
    - learning rate
    - decay schedule
    - update type
    - regularization
    - network architecture
    - number of hidden unit
    - number of depth
    - etc