# Lecture 7

- Zero centering 및 Normalization을 하는 이유 : 손실함수가 가중치의 변동에 덜 민감하게 되어 최적화하기 더 쉬움
    
    <img width="446" alt="Untitled" src="https://user-images.githubusercontent.com/99728502/235842188-62edfa47-7f7e-480c-a742-fa8d09fb4767.png">


# Fancier Optimization

- Problems with SGD
    
    <img width="508" alt="Untitled 1" src="https://user-images.githubusercontent.com/99728502/235842211-8ab94d2e-3ac5-46f9-ac36-97bf7060bc61.png">

    - 만약 한 방향으로는 Loss가 빠르게 변하는반면 다른 방향으로는 Loss가 느리게 변한다면?
        - 다시말해 Loss는 수직방향의 가중치 변화에 더 민감히 반응(수평방향보다)
        - 이 상황 속에서 SGD를 수행하면, 위의 그림과 같은 지그재그 형태를 볼 수 있음
        - Loss에 영향을 덜 주는 수평방향의 차원은 천천히 변하는 반면, 수직방향은 급격하게 변하게 됨
        - 이러한 불균형 문제는 고차원 공간에서 훨씬 쉽게 일어남
    - Local Minima & Saddle point
        
        <img width="224" alt="Untitled 2" src="https://user-images.githubusercontent.com/99728502/235842235-43c04b00-4c6a-4416-adcb-7582eca7782b.png">

        
        - 중간에 위치한 Valley에 멈춤(Locally Gradient가 0이기 때문에)
        - Saddle point는 Valley 형태는 아니지만 Gradient는 0이거나 가까운 지점. 여기서도 멈추게 됨
        - 아주 큰 모델일 경우 Saddle point의 문제가 더 심각. Gradient가 0에 가까운 지점에서 학습속도가 매우 느려짐
    - Loss를 계산할 때는 전체 Train셋에 대해 계산해야하기 때문에 시간이 오래걸림
        - 실제로는 일부 데이터만 가지고 추정한 Loss값을 사용하기 때문에, 정확한 Gradient를 측정할 수 없음
- 이러한 문제들을 해결하기 위해 Fancier 한 Optimizer가 필요

### SGD + Momentum

$v_{t+1} = \rho v_t + \nabla f(x_t) \\ x_{t+1} = x_t - \alpha v_{t+1} \ \ , \rho$ : velocity의 영향력

```python
vx = 0
while True:
	dx = compute_gradient(x)
	vx = rho*vx + dx
	x += learning_rate * vx
```

- Gradient의 velocity를 유지
- 현재 미니배치의 gradient 방향 뿐 아니라 velocity도 반영
- 물리적으로 공이 떨어지는 것을 상상할 수 있음
    
    <img width="521" alt="Untitled 3" src="https://user-images.githubusercontent.com/99728502/235842244-3d2fe688-416f-4a38-ae3d-9b9d98a561c3.png">

    - 공은 내려올 수록 빨라짐
    - gradient가 0인 평지에 마주하게 되더라도, 공의 관성으로 인해 내려올 수 있음(Local Minima 또는 Saddle point 극복)
    - 지그재그로 움직이는 상황이라면 momentum이 이 변동을 서로 상쇄시켜 버림
    - 하지만 Velocity와 Gradient가 함께 움직이기 때문에 Overshooting의 문제점 존재
    
    ### Nestrov Momentum
    
    <img width="440" alt="Untitled 4" src="https://user-images.githubusercontent.com/99728502/235842257-02735dad-4f59-475e-9dcb-b72f2e8c5d50.png">

    - SGD Momentum은 현재 지점에서의 gradient를 계산한 뒤에 velocity와 섞어줌
    - Nestrov는 우선 velocity 방향으로 움직이고, 그 지점에서의 gradient를 계산하여 둘을 합침
    - velocity의 방향이 잘못되었을 경우의 현재 gradient의 방향을 이용해 상쇄시킬 수 있도록 함
    - “현재”의 velocity와 gradient를 함께 이용하느냐(SGD), 현재의 velocity를 더한 지점에서의 gradient를 이용하느냐(Nestrov)
    - Nestrov는 Convex Optimization(볼록함수)에서는 성능이 훌륭하지만, Neural Network처럼 non-convex problem에서는 성능이 보장되지 않음
    - $v_{t+1} = \rho v_t - \alpha \nabla f(x_t + \rho v_t) \\ x_{t+1} = x_t + v_{t+1}$
    

### AdaGrad

```python
grad_squared = 0
while True:
	dx = compute_gradient(x)
	grad_squared += dx ** 2
	x -= learning_rate * dx / (np.sqrt(grad_squared) + 1e-7)
```

- Momentum 방식의 velocity term 대신에 squared term을 사용
- 학습 도중에 계산되는 gradient의 제곱을 계속해서 더해주고, Update를 할 때 Update term을 앞서 계산한 gradient 제곱항으로 나누어줌
- Gradient의 값이 큰 차원에서는 gradient의 제곱값 합이 크므로 감속하게 되고, Gradient의 값이 작은 차원에선 반대로 가속도가 붙게되어 빠르게 학습할 수 있다.
- 하지만 step을 진행할수록 Gradient의 제곱이 계속해서 더해지기 때문에, step size를 점점 더 작은 값으로 유도함
- convex case에서는 minimum 값에 접근하면 속도를 줄여 좋은 특성이 되지만, non-convex case에서는 문제가 될 수 있음(Saddle Point에서 멈출 수 있다!)

### RMSProp

```python
grad_squared = 0
while True:
	dx = compute_gradient(x)
	grad_squared = decay_rate * grad_squared + (1-decay_rate) * dx ** 2
	x -= learning_rate * dx / (np.sqrt(grad_squared) + 1e-7)
```

- AdaGrad의 변형
- gradient의 제곱값을 그저 누적시키기만 하는 것이 아니라 기존의 누적 값에 decay_rate 값을 곱해준 뒤 제곱값에 (1-decay_rate) 값을 더해줌(감가상각처럼)
- 이 특징으로 Step이 진행됨에 따라 속도가 느려지는 문제를 해결

### Adam

```python
first_moment = 0
second_moment = 0
while True:
	dx = compute_gradient(x)
	first_moment = beta1 * first_moment + (1-beta1) * dx
	second_moment = beta2 * second_moment + (1-beta2) * dx**2
	first_unbias = first_moment / (1-beta ** t)
	second_unbias = second_moment / (1 - beta2 ** t)
	x -= learning_rate * first_unbias / (np.sqrt(second_unbias) + 1e-7))
```

- Momentum 방식과 AdaGrad / RMSProp 방식을 섞음
- First moment는 velocity를 담당
- 초기 Step에서는 second moment를 0으로 초기화. 첫 단계를 거쳐도 여전히 0에 가까운 값이 되고, 그로인해 초기 step이 엄청나게 커짐(second moment를 0으로 초기화했기 때문에 발생하는 문제)
- 위의 문제를 해결하기 위해 bias correction term을 추가
- 여전히 한계점이 존재
    - 만약 타원형이 회전되었다면? (x축/y축으로만 단순히 따라갈 수 없다면)

### Model Ensemble

1. 독립적인 다양한 모델을 훈련
2. Test 시간에 그들의 결과를 평균내어 결정

모델 여러가지를 사용하지 않고, 하나의 모델을 학습하는 도중 중간모델들을 저장(snapshot)하고 앙상블로 활용하는 방법도 존재

하지만 앙상블 기법은 Test 시간에 여러개의 모델에 데이터를 넣어야 하므로 시간이 오래걸린다…

# Regularization

- 단일 모델의 성능을 끌어올리기 위한 기법
- 모델이 훈련 데이터에 완전히 fit 하는 것을 방지

### Dropout

<img width="373" alt="Untitled 5" src="https://user-images.githubusercontent.com/99728502/235842291-b6745c56-38c5-439c-9033-ca3f34b82253.png">

- Forward pass 과정에서 임의로 일부 뉴런을 0으로 만드는 것
- Forward pass를 할 때마다 0인 뉴런이 변화
- 네트워크가 일부 feature에만 의존하지 못하게 해줌. 즉 다양한 feature을 활용해서 예측할 수 있도록 하기 때문에 Overfitting을 방지
- 단일모델로 앙상블 효과를 가질 수 있음 (서로 파라미터를 공유하는 서브네트워크 앙상블을 동시에 학습시키는 것)
- Test time에는 기댓값에 dropout 확률을 곱해주어 general 하게 기댓값으로 계산 (Test set에 dropout을 적용하지는 않는다)
    - 다시 말해, dropout 된 조합별로의 확률과 그 조합에서의 연산결과를 곱한 값을 더해주어 여러 dropout 조합의 기댓값으로 결과를 출력 → 앙상블이라는게 이러한 의미임. 여러 조합 모델간의 앙상블

### Batch Normalization

- BN(Batch normalization)의 경우 test time에서는 정규화를 미니 배치 단위가 아닌 global 단위로 수행하여 stochasticity를 평균화시킴
- 이런 특성 때문에 BN은 Dropout과 유사한 Regularization 효과를 볼 수 있음

### Data Augmentation

- 데이터에 변형을 주어 데이터를 증식시키는 방법
- Horizontal Flip, Random Crop, Color jittering, rotation, stretching 등등
- 이 또한 train time에 stochasticity가 추가되어 Regularization 효과가 나타남

- 이 외에도 Drop Connect, Fractional Max Pooling, Stochastic Depth 등의 방법이 존재
    - Drop connect : Dropout과 유사하게 뉴런간 연결을 랜덤하게 끊음
    - Fractional Max Pooling : 랜덤으로 선정된 영역에 pooling 연산
    - Stochastic Depth : Dropout과 유사하게 일부 층을 제거하고 학습

- 일반적으로는 BN을 가장 많이 사용
- 하지만 Overfitting이 발생한다 싶으면 Dropout과 같은 다양한 방법을 추가할 수 있음

# Transfer Learning

- 더 큰 범주를 분류하기 위해 학습된 모델을 가져와서 작은 범주의 데이터를 분류할 수 있도록 하는 방법
- 예를 들어 1000개의 범주를 분류할 수 있는 ImageNet 데이터에 대해 학습된 모델이 존재
    - 이 모델에서 Output Layer을 제거하고, 10개의 범주에 맞도록 새로운 Output Layer을 정의
    - Output Layer을 제외한 나머지 Layer는 모두 freeze 한 뒤, 10개의 범주만 가진 데이터셋에 대해 또다시 학습
    - 이런 방식으로 학습하면 아주 작은 데이터셋이라도 잘 동작하도록 만들 수 있다.
    - 만약 데이터가 조금 더 있다면, 학습시키는 Layer을 더 늘릴 수도 있으며(이것이 Fine-tuning : 이미 학습된 모델에 일부 Layer만 새로 학습) 모델 전체를 다시 학습시킬 수 있음
