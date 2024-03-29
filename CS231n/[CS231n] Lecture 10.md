# Lecture 10

## Recurrent Neural Network(RNN)

<img width="693" alt="Untitled" src="https://github.com/3umin/Study/assets/99728502/2d08ddb3-d579-4630-8288-f44b29852529">

딥러닝 모델의 전형적인 구조

- One to many : Image Captioning(이미지에 대한 설명 출력)
- Many to one : Sentiment Analysis(감성 분석)
- Many to many(좌측구조) : Machine Translation
- Many to many(우측구조) : Video classification 등

- RNN은 입/출력이 변동적일 때 유용하게 사용되며, 입/출력이 고정된 길이여도 가변 과정(processing)인 경우에 유용히 사용

- 일반적으로 RNN은 작은 Recurrent Core Cell을 가지고 있음
    - 내부에 hidden state를 가지고 있음
        
        <img width="697" alt="Untitled 1" src="https://github.com/3umin/Study/assets/99728502/a167beac-6cfd-49fa-8024-bc26d4a6ccff">

    - 이전 hidden state의 값 $h_{t-1}$과 현재의 input 값 $x_t$를 입력으로 받아 새로운 hidden state 값 $h_t$를 출력
    - RNN의 hidden state를 재귀적으로 feed back
    
    <img width="609" alt="Untitled 2" src="https://github.com/3umin/Study/assets/99728502/e2283a46-eb45-48b5-94f6-d107a0fb7dce">

- RNN의 구조를 도식화한 그림
    - 매번 동일한 가중치 행렬 W가 사용되는 점이 특이

<img width="686" alt="Untitled 3" src="https://github.com/3umin/Study/assets/99728502/88e4a699-9e51-44a4-9791-205cefaf41e8">

- Seq2seq : Many-to-one의 Encoder 모델에 One-to-many의 Decoder 모델로 구성(Encoder-Decoder 모델)
    - 가변 입력과 가변 출력이 가능
    - Encoder에서 요약한 하나의 벡터를 바탕으로 Decoder에서 여러개의 Sequence로 출력

- RNN은 Language Modeling 등을 위해 사용
    
    <img width="310" alt="Untitled 4" src="https://github.com/3umin/Study/assets/99728502/30b09e2e-fda9-46ba-9247-075acd9eeb2b">

    여기서 모델의 예측값으로 argmax prob. 만 사용하는 방식을 취할 수도 있지만, 이 경우는 확률분포에서 샘플링하는 방식으로 예측(모델의 다양성)
    
- Backpropagation through time
    - forward pass의 경우 전체 시퀀스가 끝날 때까지 출력값 생성
    - backward pass에서 전체 시퀀스를 가지고 loss를 계산
    - 시퀀스가 아주 긴 경우에는 gradient를 계산하기 위해 전체 결과가 나올 때까지 기다렸다가 backward를 해야하기 때문에 너무 오래걸림
        
        <img width="675" alt="Untitled 5" src="https://github.com/3umin/Study/assets/99728502/9f033f08-adba-4ee6-a355-61d895f68b49">

    - 실제로는 truncated backpropagation이라는 방법을 통해 backprop을 근사시키는 기법을 사용
    - Train time에 한 스텝을 일정 단위로 잘라 Gradient Update하는 방법
    

<img width="706" alt="Untitled 6" src="https://github.com/3umin/Study/assets/99728502/5bccceb6-8f04-4a60-addc-e2c7fa90357e">

- RNN의 개별 Cell은 해석하기 힘든 Cell도 있지만, 
마침표를 찾는 셀
문장 길이 트래킹 셀
 if 문 셀
comments 셀
등등의 셀이 존재
- 그저 다음 문자를 예측하는 모델을 학습시켰을 뿐인데, 결국 모델은 입력데이터의 구조를 파악하게 학습되었다

### RNN in CV → Image Captioning

<img width="454" alt="Untitled 7" src="https://github.com/3umin/Study/assets/99728502/a679dd1f-d3de-47a8-8409-13d897e066bd">

- Image Captioning : 이미지를 입력하면 그와 관련된 설명(글)을 출력하는 Task
- ConvNet으로 이미지 연산을 거친 뒤 FC를 RNN Layer와 연결해 설명을 출력

### Image Captioning with Attention

<img width="413" alt="Untitled 8" src="https://github.com/3umin/Study/assets/99728502/e21f52b6-0206-4bf4-a505-964b11b7d3f8">

- RNN에서 더 나아가서 Attention을 사용한 Image Captioning도 있음
    - CNN으로 각 벡터가 공간정보를 가지고 있는 Grid of Vector를 생성
    - Forward pass시 매 스텝 vocab에서 샘플링 할 때, 모델이 이미지에서 보고 싶은 위치에 대한 분포를 만들어냄
    - 이미지의 각 위치에 대한 분포는 Train time에 모델이 어느 위치를 봐야하는 지에 대한 'Attention'이라 할 수 있음
    - 첫번째 hidden state(h0)은 이미지의 위치에 대한 분포(a1)을 계산하고 이 분포를 다시 벡터집합과 연산하여 이미지 attention(z1)을 생성
    - 다음 hidden state(h1)에 이 데이터들이 들어간 뒤 vocabulary의 분포(d1)과 이미지 위치에 대한 분포(a2)를 생성
    - 즉 이미지에서 보고싶은 위치에 대한 분포와 그에 대한 단어 설명을 계속해서 생성하며 나아감
    

### Visual Question Answering with Attention

<img width="457" alt="Untitled 9" src="https://github.com/3umin/Study/assets/99728502/0b5fd585-18e5-486b-8604-1ccfd0211464">

- 사진을 바탕으로 QA를 수행

### Problems with RNN

<img width="229" alt="Untitled 10" src="https://github.com/3umin/Study/assets/99728502/7d180a27-432e-4867-96f5-23fd4bf70798">

- Backpropagation 때, tanh gate에 대해 미분을 한 뒤 matrix multiply gate에 대한 미분을 하게 되는데,
- 이 mat mul gate의 backprop은 결국 가중치 행렬의 전치행렬을 곱하는 것과 동일
- 매 RNN Cell마다 가중치 행렬의 일부를 계속 곱하게 되기 때문에, 여러 Cell로 구성된 RNN의 경우 h0에 대한 Gradient를 계산하기 위해선 아주 많은 가중치 행렬이 개입하게 됨
- 가중치 행렬을 계속 곱해주기 때문에 Gradient가 Vanishing(0으로 수렴)하거나 Exploding하게 됨
- 결과적으로 Exploding gradients와 Vanishing gradients 문제가 발생하게 된다(이를 해결하기 위해 gradient clipping이라는 방법을 사용하긴 하지만, 그리 좋진 않다)

이를 해결하기 위해 LSTM이 탄생

## LSTM(Long Short Term Memory)

<img width="389" alt="Untitled 11" src="https://github.com/3umin/Study/assets/99728502/6dcca690-4fa8-4e7c-9f6c-833577163f49">

- vanishing & exploding gradients 문제를 해결하기 위해 등장
- 한 Cell당 두개의 hidden state가 존재
    - $h_t$ : RNN의 hidden state와 유사한 기능 (장기기억)
    - $c_t$ : Cell state, 단기기억을 담당
- i, f, o, g gate가 input($h_{t-1}, x_t)$을 이용해 연산을 수행하고, C를 업데이트 하는데 이용. 그리고 C로 H를 업데이트
    - f : Forget gate (whether to erase cell) - sigmoid
    - i : Input gate (Whether to write to cell) - sigmoid
    - g : Gate gate (How much to write to cell) - tanh
    - o : Output gate (How much to reveal cell) - sigmoid
- $c_t = f *c_{t-1} + i*g$
    - Forget gate로 이전의 기억을 지울지 결정한 뒤
    - Input gate와 Gate gate로 새로운 기억을 저장할지, 저장하면 얼마나 저장할지를 지정
- $h_t = o *tanh(c_t)$
    - Output gate로 cell state를 얼마나 노출할지 결정한 뒤 계산
        
        <img width="449" alt="Untitled 12" src="https://github.com/3umin/Study/assets/99728502/1c70e8ca-a4df-465b-84ac-5a191460f9de">

- Cell state에 대해 backprop을 할 때는 단순히 upstream gradient x forget gate가 됨
    - 그로 인해 연산이 matrix multiplication이 아닌 element-wise로 수행해 훨씬 빠르다
    - 매 스텝 다른 값의 forget gate를 곱하기 때문에, vanishing/exploding gradient 문제를 해결
