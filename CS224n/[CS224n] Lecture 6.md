# [CS224n] Lecture 6

# RNN Language models

## Training RNN

![6-1.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-1.png)

1. 각 단어를 RNN model에 input으로 주고, 모든 단계에서 예상되는 다음 단어를 계산
2. 모든 단계에서 예상되는 다음 단어와 실제 다음 단어간 차이의 cross-entropy를 통해 loss 계산
3. 모든 단계에서의 loss의 평균을 통해 전체 loss 계산

- 하지만 전체 문장에 대해 각각의 loss와 gradient를 계산하는 것은 너무 오래걸림
- 실제로는, $x^{(1)}, \ ... \ , x^{(t)}$ 를 각각 문장(또는 문서)로 두고 계산

## Training the parameters of RNNs : Backpropagation for RNN

![6-2.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-2.png)

Gradient를 구하기 위해 multivariable chain rule 적용

- ${d\over dt} f(x(t), y(t)) \ = \ {\partial f \over \partial x} {dx \over dt} + {\partial f \over \partial y} {dy \over dt}$

![6-3.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-3.png)

- **Backpropagation through time(BTT)**
    - 각각의 timestep별로 역전파를 실시하여 gradients를 계속 더 해나감
    - 실제로는, 20 타임스텝이 넘어가면 훈련 효율 문제로 생략하기도 함

## Evaluating language models

![6-5.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-5.png)

- perplexity : language model을 통해 예측한 corpus의 역을 corpus 길이로 정규화 해준 값
- cross-entropy에 로그를 씌우고 exponential을 씌워서 구함, 값이 낮을수록 좋음

## Language Modeling의 중요성

- 언어 이해의 진척을 측정하는데 활용할 수 있는 척도
- 많은 NLP 작업의 하위요소 : 특히 텍스트 생성 또는 텍스트 확률 추정을 수반하는 작업
- Language model NLP의 거의 모든 것을 포함하도록 확장됨

## Problems with Vanishing and Exploding Gradients

- RNN 네트워크를 학습시키기 위해 BPTT를 사용
- Backpropagation을 하기 위해 chain rule을 매 노드에 적용

![6-6.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-6.png)

![6-8.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-8.png)

- 계속 나아갈 수록 gradient는 계속해서 작아짐 → 모수에 큰 영향을 못 줄 수 있음
    
    → vanishing gradient
    

![6-9.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-9.png)

- 만약 Wh가 어느정도 작다면, 기하급수적으로 문제가 발생
- 시퀀스의 길이가 길어질 수록 gradient가 거의 없는 것만큼 작아지게 됨

### Why is Vanishing gradient a problem?

- 파라미터들이 가까이 위치한 dependency에 위주로 학습을 하고, 멀리 떨어진 dependency에 대해서는 학습을 하지 못함(long-term effects보다 near effects에 관해 update)
- Gradient는 미래에 과거가 얼마나 영향을 미치는지에 대한 척도.
    - Gradient값이 너무 작아져서 소실되어 버리는 경우에, 결과적으로 gradient값이 0이 된 이유가 ‘정말로 미래에 과거가 영향을 미치지 않아서’ 인건지, ‘파라미터 값이 잘못 설정되어서’ 인건지 구분하기 힘듬
    

### Vanishing gradient의 해결 방법

![6-12.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-12.png)

- vanilla RNN에서 hidden state가 자주 고쳐쓰게 되어 정보 유지가 어려움
    
    → RNN에서 메모리를 분리하여 나중에 사용할 수 있게 따로 정보를 저장(LSTM)
    

### Effect of vanishing gradient on RNN-LM

![6-10.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-10.png)

- 긴 문장이 input으로 들어왔을 때, 마지막에 올 단어가 ticket이라는 것을 첫번째 줄의 ticket으로 유추할 수 있지만, "vanishing gradient" 문제로 멀리 떨어진 단어들과의 dependency를 학습하지 못하게 되고, 마지막 단어로 ticket이 아닌 가까이 있는 printer로 잘못 유추

## Exploding gradient

![6-11.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-11.png)

- SGD update의 단계가 너무 커져서 발생
- 해결방법
    - Gradient Clipping
        - gradient가 일정 기준점을 넘어가면 gradient값의 L2 norm값으로 나눠주는 방식
        - gradient가 정상적인 값보다 너무 크다고 판단되었을 때, scale down을 해주는 방법

# Long Short-Term Memory(LSTM)

- RNN에서 나타나는 Vanishing gradients의 해결책
- Recurrent model에 대해 하나의 hidden vector를 가지는 것이 아니라, hidden state(h)와 cell state(c) 2개의 hidden vector을 가진채 학습

- t번째 단계에서, hidden state($h^{(t)}$)와 cell state($c^{(t)}$)가 존재
    - 두 벡터의 길이는 모두 n
    - cell state는 장기적인 정보를 저장(마치 인간두뇌의 장기기억 기능과 유사)
    - LSTM은 cell로부터 정보를 읽거나, 지우거나, 새로 작성할 수 있음

- 어떤 정보가 삭제/작성/읽기 되는지는 그에 합당한 gates에 의해 결정
    - gates 벡터의 길이는 n
    - 각각의 타임스텝에서, gates는 open(1), closed(0) 사이의 값을 가짐(1에 가까울수록 오픈)
    - gates의 값은 문맥에 의해 계산되며, 동적으로 변화함

![6-13.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-13.png)

- Forget gate : 어떤 정보를 잊고 어떤 정보를 반영할지 결정
    - t번째 시점에서의 x값과 t-1 시점에서의 hidden state를 받아 sigmoid activation function을 통해 0~1 사이 출력
    - 만약 0에 가깝다면 불필요한 정보를 다 지워버린다는 뜻, 1에 가까울수록 정보 반영 많이한다는뜻
- Input gate: 새로운 정보가 cell state에 저장될지를 결정하는 gate
- Output gate : 출력값을 반환(0~1사이 → cell state의 어느 부분을 output으로 활용할지 결정)

### Gradient 문제의 해결법

- Gradient 문제는 RNN 뿐 아니라 feed-forward와 나선형 구조를 활용하는 신경구조에서 발생
    - Chain Rule, 비선형 함수 사용 등의 이유로 역전파할 때마다 gardient가 매우 작아짐
- 해결방법
    
    1) Residual connections ‘ResNet’(= skip-connections)
    
    ![6-15.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-15.png)
    
    - input x에 나선형 layer을 지나고 나온 결과를 더해줌으로서, 과거의 내용을 기억
    - 과거의 학습내용 보존 + 추가학습 → gradient vanishing 해결

2) Dense connections ‘DenseNet’

![6-16.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-16.png)

- 이전 layer들의 feature map을 계속해서 다음 layer의 입력과 연결하는 방식

3) Highway connections "HighwayNet"

![6-17.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-17.png)

- Residual connections과 비슷
- T: transform gate / C: carry gate
    
    => output이 input에 대해 얼마나 변환되고 옮겨졌는지 표현함으로써 해결
    

## Bidirectional and Multi-layer RNNs: motivation

### Bidirectional RNNs

![6-18.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-18.png)

![6-19.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-19.png)

![6-20.PNG](%5BCS224n%5D%20Lecture%206%202d793348e38b4d59808887a75828b0dc/6-20.png)

- Bidirectional RNN : Left, Right 두 방향으로 모두 정보를 이용하기 위한 방법
    - 분리된 weight를 가지고 있는 Forward RNN과 Backward RNN을 학습한 후 각 hidden state를 병합해서 최종적인 representation 형성
    - forward RNN : 정방향으로 입력받아 hidden state 생성
    - backward RNN: 역방향으로 입력받아 hidden state 생성
    - 두 hidden state를 연결해서 전체 모델의 hidden state로 사용