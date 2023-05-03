# Lecture 8

- 매년 계속해서 바뀌는 내용 → 간단하게만 살펴보자

## CPU vs GPU

- CPU(Central Processing Unit)
- GPU(Graphics Processing Unit)
- GPU는 컴퓨터 그래픽을 렌더링하기 위해 만들어짐(게임 등)
- 딥러닝의 경우 AMD 대신 NVIDIA를 거의 사용(딥러닝 학습에 있어서 NVIDIA가 거의 독점적)
- GPU의 많은 코어 때문에 어떤 Task가 주어졌을 때 이를 병렬로 수행하기 쉬우며, GPU의 메모리가 CPU의 캐시메모리보다 훨씬 커서 다량 연산에도 유리
- GPU에서 아주 잘 작동하는 연산은 Matrix Multiplication(병렬 연산 때문에)

## Programming with GPUs

- CUDA(NVIDIA Only)
    - 유저가 효율적인 CUDA 코드를 짜는 것은 어렵기 때문에, NVIDIA에서 CUDA 코드로 변환하기 위한 효율적인 라이브러리를 제공(cuBLAS, cuFFT, cuDNN 등)
- OpenCL
    - 딥러닝에 극도로 최적화된 연산이나 라이브러리가 부족해, CUDA에 비해 성능이 떨어짐

## CPU / GPU Communication

- GPU로 학습을 진행할 때, 모델과 모델의 가중치는 모두 GPU의 RAM에 저장된 반면 학습을 진행하는 Data는 SSD나 HDD에 저장되어 있음
- 그렇기 때문에 Train time에 디스크에서 데이터를 읽어들이는 작업을 신경쓰지 않으면 보틀넥이 발생
    - 해결법
        - 데이터셋이 작거나 RAM이 감당가능할 경우, 모든데이터를 RAM으로 불러오기
        - HDD 대신 SDD를 사용
        - CPU의 멀티쓰레드를 사용해 데이터를 RAM에 미리 올려놓는 것(pre-fetching)

# Deep Learning Frameworks

- (17년 기준) Tensorflow가 메인 프레임워크가 됨
    - 지금은 토치가 뜨고 있는듯..
- 초기 딥러닝 프레임워크는 학술 연구팀에서 개발됨
- 하지만 다음 세대의 프레임워크는 일반 기업에서 개발
- 강의에선 Caffe와 Caffe2, PyTorch, TensorFlow를 다룸
- 딥러닝 프레임워크를 사용해야 하는 이유
    - 쉽게 큰 Computational Graph를 만들 수 있음
    - 쉽게 gradient를 계산할 수 있음
    - GPU에서 효율적으로 돌아감

<img width="533" alt="Untitled" src="https://user-images.githubusercontent.com/99728502/235842395-24d5ff19-2da1-46dc-a8c7-d156df38ec40.png">

- Numpy로 딥러닝을 구현하면, Gradient를 계산하는 코드를 직접 짜야하며 또한 GPU에서 돌아가지 않는다.
- 하지만 Tensorflow와 같은 딥러닝 프레임워크에서 사용하면, 그래디언트를 계산해주는 함수가 있음
- 또한 GPU로 돌릴지 CPU로 돌릴지 편하게 전환할 수 있음

# TensorFlow

<img width="533" alt="Untitled 1" src="https://user-images.githubusercontent.com/99728502/235842406-c0361527-c752-4570-9f8a-8409c38aa02d.png">

- Tensorflow의 경우에는 코드가 크게 두가지로 나뉨
    - Computational Graph를 정의하는 부분, 연산을 수행하는 부분
    - 정의하는 부분에서는 Graph를 정의하기만 하고, 데이터가 들어왔을 때 연산을 어떻게 수행해야할 지를 정의
    - 모델을 학습할 때, GPU에서 계산한 값을 CPU(Numpy)로 보냈다가 다시 GPU로 보내는 과정에서 보틀넥이 발생. 이를 방지하기 위해 tf.Variable로 그냥 tensorflow의 변수로 선언
        
        <img width="530" alt="Untitled 2" src="https://user-images.githubusercontent.com/99728502/235842419-e062d9c6-c13c-4c23-9f8f-24d3181961e3.png">

    - Numpy array로 끄집어내지 않고 텐서플로 연산으로 계속 수행해 GPU 내에서 연산이 이어지도록 유도

- TensorFlow에는 gradient를 계산하고 가중치를 update해주는 Optimizer라는 기능
- 입력과 가중치를 정의하고 행렬곱 연산으로 묶는 것 또한 한 번에 진행해줄 수 있음
    - tf.layers로 layer을 추가해주어 알아서 Wx + b 연산을 수행하도록 해줄 수 있음
    - 이러한 변수들은 Xavier Initilization을 통해 초기화

- Keras : Tensorflow를 backend로 해서 computational graph를 알아서 만들어줌
    
    <img width="526" alt="Untitled 3" src="https://user-images.githubusercontent.com/99728502/235842436-aef65ae3-9629-4a3b-bc27-503f649dec68.png">

    - tf로는 상당히 길었던 코드를 keras를 이용해 아주 간단하게 사용할 수 있다!

# PyTorch

- 파이토치는 3가지의 level로 구성
    - Tensor : Numpy array와 유사하지만 GPU에서 돌아감
    - Variable : Computational Graph의 노드. 데이터와 gradient를 저장
    - Module : 신경망 layer, state나 학습 가능한 가중치를 저장

### PyTorch : Tensors

<img width="504" alt="Untitled 4" src="https://user-images.githubusercontent.com/99728502/235842448-600dcc1d-2df8-4035-86bf-8aba7cb7b084.png">

- Numpy array를 사용하지 않고 PyTorch Tensor로 값을 정의
- GPU를 사용하기 위해선 `torch.FloatTensor`가 아니라 `torch.cuda.FloatTensor` 을 사용(1.0 기준)

### PyTorch : Variable

<img width="527" alt="Untitled 5" src="https://user-images.githubusercontent.com/99728502/235842461-22a14e81-32da-4ae4-b91f-9db8269db249.png">

- PyTorch의 Variable과 Tensor는 서로 호환
- Tensor는 imperative 한 연산자들, Variable은 Computational Graph와 연관
- Variable을 선언할 때 해당 Variable에 대한 Gradient를 계산할지 여부를 지정할 수 있음
- 파이토치와 텐서플로의 차이점
    - Tensorflow는 그래프를 명시적으로 구성한 다음에 그래프를 돌림
    - PyTorch는 forward pass를 할 때마다 매번 그래프를 다시 구성

### PyTorch : nn

<img width="527" alt="Untitled 6" src="https://user-images.githubusercontent.com/99728502/235842476-cc973eed-36d5-41ac-873c-8af2240cf653.png">

- TensorFlow의 Keras처럼, 고수준의 wrapper를 담당하는 기능을 PyTorch에선 nn 패키지가 담당.
- PyTorch에서도 Optimizer를 사용 가능
    - `torch.optim.`

- PyTorch를 사용할 때, 전체 네트워크 모델이 정의되어 있는 Class를 nn module class로 작성해야 함
    
    <img width="529" alt="Untitled 7" src="https://user-images.githubusercontent.com/99728502/235842484-e9f3de7d-6dc7-41c1-9593-0efe1f9df1c0.png">

    - 2개의 linear layer, forward pass 방법(activation fun) 등을 정의한 Class로 자체 모듈을 생성
    - 이후 Optimizer를 구성하고 반복문을 돌면서 데이터를 넣어주고, Backwards로 Gradient를 구한 뒤 step으로 업데이트 하는 과정을 통해 학습

- DataLoader를 통해 batch size에 맞게 데이터를 로드시켜주는 기능도 있음
    - DataLoader의 내부에서 data shuffling이나 multithreaded data loading과 같은 문제를 알아서 관리
- Tensorflow의 Tensorboard와 유사하게, PyTorch에선 Visdom이 존재

# Static vs Dynamic Graphs

<img width="532" alt="Untitled 8" src="https://user-images.githubusercontent.com/99728502/235842492-0a833ecc-ed51-4ea7-9907-25530d5c1a88.png">

- Tensorflow의 경우, 그래프를 구성한 뒤 이 그래프를 반복적으로 돌림 → Static Computational Graph
- PyTorch의 경우, 매번 forward pass를 할 때마다 새로운 그래프를 구성 → Dynamic Computational Graph

### Static Graph

<img width="514" alt="Untitled 9" src="https://user-images.githubusercontent.com/99728502/235842513-a946f010-a50a-42c4-b94b-5cbbbd55fa7e.png">

- 그래프를 한 번 구성해놓으면 그 그래프를 계속해서 사용
- 그렇기 때문에 그래프를 최적화할 수 있는 기회가 존재
- 일부 연산을 합쳐버리고, 재배열하는 등 효율적인 연산을 하도록 최적화할 수 있음
- 첫 최적화 작업이 오래걸릴 순 있지만, 그 그래프를 여러번 사용하기 때문에 시간을 절약할 수 있다
- 위의 예시는 ConvNet 뒤에 ReLU가 오는 경우에 둘을 합쳐서 연산해 더 효율적으로 압축한 예시
- Static의 경우, 그래프를 한 번 구성해놓으면 메모리 내에 그 네트워크의 구조를 가지고 있어서 계속해서 모델을 불러오는 코드를 입력하지 않아도 된다는 이점이 있음

## Dynamic Graph

- Dynamic의 경우 모델을 재사용하기 위해서는 항상 코드를 다시 불러와야한다는 단점이 존재
- 하지만 대다수의 경우에 코드가 훨씬 더 깔끔하고 작성하기 쉽다
- 조건부 연산의 경우
    
    <img width="524" alt="Untitled 10" src="https://user-images.githubusercontent.com/99728502/235842520-ea70973b-4fed-4c4c-b0a7-75c6f71647f5.png">

    - 위의 예시의 경우, Torch는 연산을 수행할 때마다 계속 확인하기 때문에 간단한 python 문법인 if문 하나로 가능하지만
    - TensorFlow에서는 그래프를 하나 만들어놔야 하기 때문에 조건부 연산을 명시적으로 정의하는 부분을 넣어야 함(`tf.cond` 라는 함수로 그래프 내에 control flow를 넣어야 한다)
- 반복문의 경우
    
    <img width="532" alt="Untitled 11" src="https://user-images.githubusercontent.com/99728502/235842531-ac5e29e0-4f26-4ef3-9190-a7860c8fc26b.png">

    - 재귀적으로 반복되는 함수를 만드는 예시
    - 데이터의 sequence의 길이가 얼마인지에 관련없이, 간단한 파이썬 for문으로 가능하며 backprop에도 지장이 없음
    - TensorFlow에서는 그래프에 명시적으로 loop을 넣어줘야 함(`tf.fold1`)

- Static Graph에서는 그래프에 필요한 모든 control flow 연산 및 데이터 구조 등을 전부 구현해놔야 함
    - 이 때문에 Tensorflow에서는 Python 명령어를 같이 활용하기 어렵고, TF에서 필요한 control flow 연산들을 전부 다시 배워야한다

## Dynamic Graph Applications

- RNN
    - 다양한 길이의 sequence를 다루기 위해 dynamic 시스템을 사용
- Recursive network
    
    <img width="235" alt="Untitled 12" src="https://user-images.githubusercontent.com/99728502/235842547-f365ce44-d18f-44e6-be9f-ca888e91a59f.png">

    - 그래프를 구성할 때 데이터의 구조를 반영하도록 구성해 유동적인 네트워크 형성
- Modular Networks
    
    <img width="299" alt="Untitled 13" src="https://user-images.githubusercontent.com/99728502/235842557-794f9903-a824-46bb-9556-47e7c0cfc7ea.png">

    - 이미지와 질문을 던지면 QA를 해주는 문제
    - 질문에 맞추어 유동적으로 활용할 수 있도록 dynamic 사용
- 이처럼 dynamic graph를 이용한다면 많은 것들을 할 수 있다

# Caffe

- Caffe는 다른 딥러닝 프레임워크들과는 다르게, 기존에 빌드된 바이너리를 호출하고 Configuration 파일만 조금 손보 코드를 작성하지 않아도 학습할 수 있음
- 사용 방법
    1. 데이터를 HDF5 또는 LMDB 포맷으로 변환(Caffe에서 지원하는 스크립트를 사용)
    2. Network를 prototxt 라는 형식으로 구현
    3. Optimizer나 Solver를 정의하기 위한 prototxt를 다시 정의
    4. Train
