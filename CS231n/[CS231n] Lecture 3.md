# Lecture 3

# 손실함수(Loss Function)

- 손실함수란? 
W를 입력으로 받아서 각 스코어를 확인하고 W가 얼마나 효과적인지를 계산해주는 함수
    
    <img width="419" alt="Untitled" src="https://user-images.githubusercontent.com/99728502/226259404-80d47687-2376-49ee-8baf-5097f2e0ae9e.png">
    
- 손실함수 : $L = {1 \over N} \sum L_i(f(x_i, W), y_i)$ 로 정의
- W의 공간을 탐색하면서 L을 최소화하는 적합한 W를 찾는 방식으로 학습

### Multiclass SVM loss

- $L_i = \sum_{j \ne y_i} max(0, s_j -s_{y_i} + 1)$로 정의
    
    ```python
    def L_i_vectorized(x, y, W):
    	scores = W.dot(x)
    	margins = np.maximum(0, scores - scores[y] + 1)
    	margins[y] = 0   # 정답 스코어는 고려하지 않으므로 0으로 취급
    	loss_i = np.sum(margins)
    	return loss_i
    ```
    
    - 제대로 예측했다면(정답 클래스의 score가 가장 높다면) Loss는 0이 되고, 그렇지 않다면 0보다 큰 값이 더해지게 됨
    - 구체적으로 손실함수의 값이 몇인지는 신경쓰지 않고, 이것이 가질 수 있는 값의 범위 내에서 극소값을 가지느냐에 초점을 둠
    - loss를 제곱항으로 계산한다면, 차이가 조금 나는것은 곱절로 손실항에 들어가게 됨 
    → 심하게 잘못 분류되는 것을 정말 원하지 않을 경우 사용
    - Hinge Loss(위의 손실함수의 경우)는 조금 잘못된 것과 많이 잘못된 것을 크게 신경쓰지 않음

### Softmax(Cross-Entropy Loss)

<img width="398" alt="Untitled 1" src="https://user-images.githubusercontent.com/99728502/226259463-c9a58930-d2f4-4563-b8a4-f1eaca5f6840.png">


- $P(Y= k|X=X_i) = {e^sk \over \sum_j e^sj}, \ s = f(x_i;W)$
- $L_i = -logP(Y=y_i|X=x_i)$
- 정답 클래스의 확률이 1에 가까워지도록 노력
- Softmax 함수의 최솟값은 0, 최댓값은 1 (확률)
- Softmax Loss는 -log를 씌우기 때문에 0 ~ $\infty$ 값을 가짐
- SVM의 경우 일정 선을 넘기만 하면 더이상 성능 개선에 신경쓰지 않음. 
하지만 Softmax의 경우 더더더더좋게 성능을 향상시키고자 노력
- 어떤 손실함수를 선택하느냐는 에러에 대해 우리가 얼마나 신경쓰고 있는지에 달림

# Regularization(규제)

- Loss 함수를 통해 데이터에 fitting 시키게 된다면, 새로운 데이터엔 적합하지 않을 확률이 높음 → Regularization 항을 추가

    <img width="312" alt="Untitled 2" src="https://user-images.githubusercontent.com/99728502/226259500-08681004-b95c-4dde-aca0-4738ee5533c5.png">

- 오컴의 면도날 이론 : 비등한 가설들중엔 단순한 것이 최고
- 이를 위해 Regularization Penalty를 추가
    - 모델이 훈련셋에 완벽히 Fit하지 못하도록 모델의 복잡도에 패널티를 부여하는 방법

- Regularization
    - L2 Regularization : $R(W) = \sum_k \sum_l W^2_{k, l}$
    - L1 Regularization : $R(W) = \sum_k \sum_l |W_{k, l}|$
    - Elastic net (L1 + L2) : $R(W) = \sum_k \sum_l \beta W^2_{k, l} + |W_{k, l}|$
    - Max norm regularization
    - Dropout, Batch normalization, Stochastic depth 등등

- $x = [1, 1, 1, 1], w_1 = [1, 0, 0, 0], w_2 = [0.25, 0.25, 0.25, 0.25]$ 일 때 $w_1^Tx = w_2^Tx = 1$ 로 계산되는 값은 동일한 상황
    
    하지만 여기서 L1 Regularization과 L2 Regularization의 값은 다르다.
    
    $R_{L1}(w_1) = \sum_k \sum_l |w_1(k, l)|  =  1 + 0 + 0 + 0 = 1$

    $R_{L1}(w_2) = \sum_k \sum_l |w_2(k, l)| = 4 * 0.25 = 1$
    
    $R_{L2}(w_1) = \sum_k \sum_l w^2_1(k, l)  =  1^2 + 0^2 + 0^2 + 0^2 = 1$ 

    $R_{L2}(w_2) = \sum_k \sum_l w_2^2(k, l) = 4 * 0.25^2 = 0.25$
    
    L1 규제로 보게되면 w1과 w2의 규제값 역시 같게 되어 동일한 weight 취급을 할 수 있지만, L2 규제로 보게되면 규제값이 서로 달라 둘중 더 복잡한 w1에 penalty를 부여하게 됨.

# 최적화(Optimization)

- loss의 최소값에 도달하기 위해 진
- 임의의 지점에서 시작하여 점차적으로 성능을 향상

1. Random search(bad)
    - 임의로 샘플링한 W들을 모아놓고 loss를 계산하여 가장 좋은 것을 선택
2. Follow the slope

    <img width="398" alt="Untitled 3" src="https://user-images.githubusercontent.com/99728502/226259538-df390665-db49-4359-8da0-feef64bfb1fd.png">
    
    - 경사를 확인한 뒤 경사에 맞게 따라감
    - 특정 지점에서 미분을 통해 경사를 계산
    - Gradient Descent
        
        ```python
        while True:
        	weights_grad = evaluate_gradient(loss_fun, data, weights)
        	weights += - step_size * weights_grad   # perform parameter update
        ```
        
        - W를 임의의 값으로 초기화한 뒤 진행
        - 더이상 loss가 줄어들지 않아 수렴할 때 까지 반복
    - Stochastic Gradient Descent(SGD)
        - 데이터셋이 크다면 일일히 loss를 계산하는 과정이 너무 오래걸리므로, 일부 표본만 추출하여 gradient를 계산
        
        ```python
        while True:
        	data_batch = sample_training_data(data, 256) # 256 sample
        	weights_grad = evaluate_gradient(loss_fun, data_batch, weights)
        	weights += - step_size * weights_grad
        ```
        

- 특징 변환을 통해 이미지를 원본보다 훨씬 쉽게 구분할 수 있음
    - Color Histogram
        
        <img width="371" alt="Untitled 4" src="https://user-images.githubusercontent.com/99728502/226259556-1628c8c1-1649-40dc-a36b-f53ed7cf9855.png">

    - Histogram of Oriented Gradients
    
        <img width="373" alt="Untitled 5" src="https://user-images.githubusercontent.com/99728502/226259567-ef6c26fa-470b-4500-a56a-3d16a9d497f6.png">

        
        - 이미지내에 전반적으로 어떠한 Edge 정보가 있는지를 보여줌
    - Bag of Words(BOW)
    
        <img width="401" alt="Untitled 6" src="https://user-images.githubusercontent.com/99728502/226259580-7525dd88-a470-44a8-b59d-073268f8ca93.png">
        
        - NLP에서 따온 개념
        - 시각단어 : 이미지를 임의로 조각낸 후, K-means와 같은 알고리즘으로 조각들을 군집
        - 이미지 내의 다양한 것들을 표현할 수 있는 군집을 생성
