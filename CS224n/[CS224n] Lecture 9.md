# [CS224n] Lecture 9

# Issues with recurrent models

## 1. Linear interaction distance

![9-1](https://user-images.githubusercontent.com/99728502/226580943-63a2c3a7-f1e7-4bf9-926c-dc1977994a16.png)

- 단어의 의미는 가까운 다른 단어의 의미에 의해 영향받을 수 있음
- RNN은 left to right으로 전개됨
- 문제: 이러한 상호작용을 위해 RNN이 O(sequence length)만큼의 step을 밟아야함
    
    ⇒ gradient vanishing 때문에 멀리 떨어진 단어간의 상호작용 어려움
    

## 2. Lack of Parallelizability

![9-2](https://user-images.githubusercontent.com/99728502/226580967-f507e7be-941c-4ecd-87f5-28f1fb7632d8.png)

- 평행 가능성의 부재
- GPU는 많은 독립적인 계산을 한번에 할 수 있지만, RNN의 hidden state같은 경우 전 단계의 계산이 끝나야 다음 단계의 계산을 할 수 있음
    - ex) 2번을 계산하기 위해선 0과 1의 계산 모두 선행되여야함
    
    ⇒  많은 양의 데이터셋의 훈련을 힘들게 만듦
    

# Recurrent 모델의 대안: Word window

<img width="416" alt="9-14" src="https://user-images.githubusercontent.com/99728502/226580994-ddee9358-ee99-439e-a0a6-2d794e73e52f.png">

- Word window 모델은 주변 단어들을 종합
- Word window layer을 쌓아서 멀리 떨어진 단어간 상호작용이 이루어질 수 있음
- Maximum interaction distance = sequence length / window size

<img width="401" alt="9-15" src="https://user-images.githubusercontent.com/99728502/226581006-30d25765-1a90-42aa-8d5a-885bcd972d35.png">

- 하지만 Window size를 넘어가는 단어는 무시하기 때문에, 문장이 너무 길면 멀리 떨어진 단어를 전혀 반영하지 못함
    - ex) 위 그림에서 $h_k$를 예측할 때 $h_1$은 전혀 고려되지 않음

# Recurrent model의 대안: Attention

![9-4](https://user-images.githubusercontent.com/99728502/226581024-7d0a0432-1618-4219-8a92-07c70c7b1cfc.png)

- Attention은 각 단어의 표현을 query로 간주함
- 문장이 한번에 들어가 연산되기 때문에 문장의 길이가 연산에 상관 X

## Self-Attention

![9-5](https://user-images.githubusercontent.com/99728502/226581032-8c88d066-17b6-41fe-afb1-839c739905c0.png)

- 각 단어별로 query, key, value가 각각 계산됨
    - Query : 주어진 벡터들 중에 어떤 벡터를 선별적으로 가져올지에 대한 기준이 되는 vector
    - Key : query 벡터와 내적을 통해 유사도를 구하는 재료 vector
    - Value : 내적으로 구해진 유사도, 가중치를 결합하여 encoding을 만들 재료 vector
- 계산된 각각의 query, key, value를 바탕으로 위의 단계를 거쳐 최종적인 가중합 output 출력

<img width="393" alt="9-16" src="https://user-images.githubusercontent.com/99728502/226581058-dff1d8bf-a4ef-4ede-b836-767660f7f267.png">

- step을 밟은 뒤 또 각각의 단어별로 k q v가 계산되고, 그게 중첩되어 마치 LSTM stack layers처럼 layer 형성

### Self-Attention의 문제점

1. 문장 순서에 대한 정보 X
    
    → pi를 sequence index를 나타내는 벡터로 새로 생성
    
    → $\tilde v_i, \ \tilde k_i, \ \tilde q_i$ 를 계산
    
    $v_i = \tilde v_i + p_i$
    
    $q_i = \tilde q_i + p_i$
    
    $k_i = \tilde k_i + p_i$
    

2. 딥러닝에 필요한 비선형성의 부재

![9-7](https://user-images.githubusercontent.com/99728502/226581085-bce827a3-3a9a-4cec-836d-63c8beaa3a05.png)

- value vector에 대해 다시 평균을 계산
- feed-forward network를 각각의 output vector 처리 후에 추가
- $m_i = MLP(output_i) = W_2 * ReLU(W_1*output_i + b_1) + b_2$

1. 문장을 예측할 때 뒤에 나오는 단어를 확인하지 않는다는 증거의 부재 
    
    ![9-8](https://user-images.githubusercontent.com/99728502/226581098-333513f5-ae99-4372-ad9b-2084c7017b19.png)

- Self-Attention에서 미래에 나오는 단어를 마스킹처리

     $e_{ij} = \begin{cases}
q_i^Tk_j, \ \ \ k < j \\
 -\infin, \ \ \ k \ge j
\end{cases}$

# Transformer model

![9-9](https://user-images.githubusercontent.com/99728502/226581121-18b8a15c-5745-4de6-a99c-d6f7d275d35b.png)

- Attention based model

# Transformation Encoder

1. key-query-value attention : k, q, v 벡터를 어떻게 구하는가
2. Multi-headed attention : 하나의 레이어에 다양한 정보 입력
3. tricks to help with training
    - residual connections
    - layer normalization
    - scaling the dot product
    

### Key-Query-Value Attention

- $[x_1, ... \ , x_T]$  : Transformer 인코더에 들어가는 인풋벡터
- $k_i = Kx_i \ \in R^{d \times d}$ : key matrix
- $q_i = Qx_i \ \in R^{d \times d}$  : query matrix
- $v_i = Vx_i \ \in R^{d \times d}$  : value matrix
    
    ⇒ 이러한 행렬들은 x 벡터를 세 개의 서로 다른 측면에서 활용할 수 있도록 함
    

- $X = [x_1; ...\ ;x_T] \ \in R^{T\times d}$
- $output = softmax(XQ(XK)^T) \times XV$ 를 통해 계산

### Multi-headed Attention

![9-10](https://user-images.githubusercontent.com/99728502/226581148-24bf5a2c-f6da-4970-a896-044bc7c2e66e.png)

- 동일한 sequence가 주어졌을 때, 서로 다른 기준으로 여러 측면에서의 정보를 뽑아낼 수 있음
    - 예시) I went to the school / I studied hard / I took the rest
    ⇒ 어떤 문장에서는 I가 하는 행동을 중심으로 하는 정보
    ⇒ 어떤 문장에서는 I가 있는 장소의 정보
- Q, K, V 행렬을 여러개로 만들어서 multiple attention heads 정의

### Residual connections

<img width="381" alt="9-13" src="https://user-images.githubusercontent.com/99728502/226581179-459f4db0-d9ed-4a7b-a452-51fcdc828f76.png">

- $X^{(i)} = Layer(X^{(i-1)})$ 대신에, $X^{(i)} = X^{(i-1)} + Layer(X^{(i-1)})$로 정의
    
    ⇒ Gradient vanishing을 방지
    

### Layer normalization

- unit의 평균과 표준편차로 표준화를 진행해서 정보가 없는 hidden vector value를 제거함
- Let
    
    $\mu = \sum_{j=1}^d x_j$
    
    $\sigma = \sqrt({1\over d}\sum_{j=1}^d (x_j - \mu)^2)$
    
    $\gamma \in R^d$ : gain parameter
    
    $\beta \in R^d$ : bias parameter
    
    ⇒ $output = {x - \mu\over \sigma + \epsilon} \gamma + \beta$
    

### Scaled Dot Product

- 차원이 커짐 → 내적값의 분산이 커짐 → softmax function도 커짐 → Vanishing Gradient!
- 이를 Scaling을 통해 해결
- Let
    
    $output_l = softmax({XQ_lK_l^TX^T\over \sqrt(d/h)})* XV_l$
    

## Transformer Model

![9-11](https://user-images.githubusercontent.com/99728502/226581193-b87feb2c-3032-406b-86f2-ae79bd262c59.png)

![9-12](https://user-images.githubusercontent.com/99728502/226581212-94961244-4edc-4a00-9179-82c321a42da3.png)

- 이 모든것을 종합해보면, 인코더 블럭과 디코더 블럭은 각각 이러한 형태로 구성

- Cross-Attention(Decoder)
    - $h_1, ... \ , h_T$  : Transformer Encoder에서 나온 output vector
    - $z_1, ...\ , z_T$ : Transformer Decoder에 들어가는 input vector
    - $k_i = Kh_i \ , \ v_i = Vh_i \ , \ q_i = Qz_i$
    - $H = [h_1; ...\ ;h_T]$
    - $Z = [z_1;...\ ; z_T]$
    - $output = softmax(ZQ(HK)^T)*HV$
