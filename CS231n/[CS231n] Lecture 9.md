# Lecture 9

## AlexNet

<img width="532" alt="Untitled" src="https://github.com/3umin/Study/assets/99728502/92851255-3416-46bc-a851-6f39eacfcd6b">

- 최초의 Large scale CNN, 2012 Imagenet Challenge 우승 모델
- Conv - Pool - normalization 층이 반복
- Input : 227 x 227 x 3 size
- First Layer : 11 x 11 size의 96개 filters with stride = 4
    - Output size = ${N-F \over Stride} + 1 = {227 - 11\over4} + 1 = 55$
    - 55 x 55 x 96 사이즈의 output 출력
    - → 첫번째 layer에는 11 x 11 x 3 x 96 만큼의 파라미터가 필요
- Second Layer : 3 x 3 size의 Pooling Layer with stride = 2
    - Output size = ${55-3\over2}+1=27$
    - 27 x 27 x 96 size의 output
    - → Pooling layer에는 파라미터가 없다
- 등등의 구조를 거치고, 3개의 FC으로 최종 연결

### AlexNet의 특징

- 최초로 ReLU 사용
- local response normalization layer 사용 (요즘엔 잘 사용하지 않음)
- flipping, jittering, croping, color normalization 등의 Augmentation 기법
- 0.5 확률의 Dropout, 128 배치사이즈, 1e-2 ~ 1e-10까지의 learning rate를 사용한 SGD momentum
- L2 weight decay 5e-4, 7개의 CNN 앙상블 구조
- 당시에는 GPU 용량의 한계로, 네트워크의 뉴런과 Feature Map을 반반씩 나누어 2개의 GPU로 훈련

## ZFNet

- 2013 ImageNet Challenge 우승 모델
- AlexNet과 구조적으로는 비슷하나, 일부만 수정하여 더 좋은 성능 기록

- 2012/2013의 AlexNet과 ZFNet은 레이어가 8개였지만,
- 2014년의 VGG와 GoogleNet은 각각 19 layer와 22 layer로 네트워크가 더 깊어짐

## VGG

[VGG](https://www.notion.so/VGG-63aa370c2b4d47b1ae96b318bf83c55d?pvs=21)

- 위는 예전에 제가 공부하다가 VGG 논문 리뷰를 한 적이 있는데 그 자료
- 네트워크가 훨씬 깊어고, 더 작은 필터를 사용
- 16~19개의 레이어를 가짐 (VGG16 → 16개의 layer라는 뜻)
- VGG는 아주 작은 필터만 사용(항상 3 x 3 사이즈)

- 작은 필터를 사용하는 이유?
    - 필터의 크기가 적으면 파라미터의 수가 더 적음
    - 3 x 3 사이즈의 필터 3개를 겹쳐놓으면 7 x 7 사이즈의 필터 1개와 동일한 feature map 크기가 출력
    - 근데 파라미터? 7 x 7 은 $49C^2$개의 파라미터를 가지는 반면 3 x 3 3개는 $3^3C^2 = 27C^2$개의 파라미터만 가지기 때문에 훨씬 줄어든다
    - 또한 이렇게 여러겹으로 쌓기 때문에 비선형성이 더욱 더 증가됨
- 하지만 이렇게 파라미터를 줄였어도 모델이 깊기 때문에 파라미터수가 138M으로 상당히 높음
    - 그렇기 때문에 메모리 사용량도 VGG16 기준 이미지당 96MB의 사용량 → 상당히 높다!

- AlexNet과 훈련 과정은 유사하지만, Local Response Normalization은 사용하지 않음

## GoogLeNet

- 22개의 레이어를 가진 깊은 네트워크
- VGG와는 다르게 높은 계산량을 효율적으로 수행할 수 있도록 네트워크를 디자인함 → Inception module
- 파라미터 수를 줄이기 위해 FC layer가 없고, 전체 파라미터 수가 5M 정도로 60M인 AlexNet보다 적다

### What is Inception module?

<img width="260" alt="Untitled 1" src="https://github.com/3umin/Study/assets/99728502/51493ba1-de62-40c3-908e-8ba2f8e700a8">

- “network within a network” 라는 개념으로 local topology를 구현했고 이를 쌓아올림
- 동일한 입력에 대해 서로 다른 다양한 필터들이 병렬로 존재
- 다양한 필터에서 출력된 다양한 출력을 모두 Depth 방향으로 합치는 방식 → 이 방법은 계산복잡성 문제가 있을 수 있음.
  
    <img width="276" alt="Untitled 2" src="https://github.com/3umin/Study/assets/99728502/cfc4519a-2168-446b-a50c-5520829239cc">

    
    - zero padding으로 필터들간의 spatial dimension을 맞춘 예제
    - 이 경우 무려 spatial dimension은 변하지 않았지만 depth가 많이 늘었고, 연산 수도 무려 854M개가 존재

- 이러한 문제를 어떻게 해결하나?
    
    <img width="429" alt="Untitled 3" src="https://github.com/3umin/Study/assets/99728502/856b265d-d330-4b34-a8d0-fdda40bd0ff2">

    - bottleneck layer을 사용
    - Conv 연산 이전에 bottleneck layer을 통해 입력을 더 낮은 차원으로 보냄
    - 1 x 1 사이즈의 Conv 층을 통해 spatial size는 유지하면서, depth를 더 낮은 차원으로 투영
        
        <img width="519" alt="Untitled 4" src="https://github.com/3umin/Study/assets/99728502/a82a785f-7c0d-4f2f-8973-f028cd876cf4">

    - 입력의 차원을 줄여서 연산횟수를 854M회에서 358M회로 줄일 수 있었음
    - GoogleNet은 이러한 Inception module들을 쌓아올려 구성
        
        <img width="492" alt="Untitled 5" src="https://github.com/3umin/Study/assets/99728502/7b27f925-c896-4a0d-b26d-d04d7a2751d4">

    - 네트워크의 끝 뿐 아니라 따로 삐져나온 저 두 곳에서도 Loss를 계산함(네트워크가 깊기 때문에 보조분류기를 중간에 달아주어 중간 레이어의 학습을 도울 수 있다)
    - 중간에 달려있는 보조분류기의 Loss 값을 바탕으로 해당 영역의 Gradient를 업데이트(최종 classifier의 loss로 처음까지 오면 Gradient가 0이 되어서 제대로 업데이트 불가..)

### GoogLeNet 특징

- 22 layer
- Efficient “Inception” module
- No FC layer
- 12 times less params than AlexNet

## RestNet

- 2015 ImageNet Challenge 우승 모델
- 152 레이어로 엄청나게 깊어진 네트워크
- Residual connection이라는 방법을 사용

### Residual connection이란?

- Motivation : 일반 CNN을 계속해서 깊게 쌓게 되면 어떤 일이 발생할 것인가?
    - 모델에 엄청나게 많은 파라미터가 있으니 Overfitting으로 예상
    - 하지만 실제로는 training error와 test error 모두 일반 모델보다 높게 나타남
        
        → 모델이 깊어질수록 최적화가 어렵다는 가설
        
        - 더 얕은 모델의 가중치를 깊은 모델의 일부 레이어에 복사해 최소한 얕은 모델만큼의 성능은 나와야할 것이다.

<img width="184" alt="Untitled 6" src="https://github.com/3umin/Study/assets/99728502/3d65075b-768c-440e-9de6-5bfb2f87e851">

- 위의 원리를 활용하기 위해, 레이어를 단순하게 쌓지 않고 Residual mapping을 하도록 블럭을 생성
    - 오른쪽의 Skip Connection은 가중치 없이 입력을 그대로 출력단으로 내보냄
    - 이렇게 되면 실제 레이어는 변화량(X에 대한 잔차)만 학습하면 됨
    - 최종 출력값은 X + Residual

- 이러한 ResNet Block을 하나씩 쌓아올리며 모델을 구성
    - 만약 Depth가 50 이상일 경우, GoogLeNet과 유사하게 bottleneck layer을 중간에 추가하여 depth를 줄여서 사용
    - 매 Conv 층 이후에 Batch Normalization을 해주고, Xavier/2 초기화, SGD 모멘텀(0.9), learning rate 0.1에서 시작해서 10씩 나누어주며 학습
    - 256 크기 Mini batch, Weight decay 1e-5 사용
    - dropout은 사용하지 않음

## Other architectures

- Deep Residual Network
    - Resnet 구조에서 Skip connection 사이의 층(Residual Block)을 더 deep하게 구성
- Wide Residual Network
    - Resnet 구조에서 Residual Block을 더 넓게(크게) 구성
        - 다시말해 layer의 뉴런의 수를 더 크게 함
    - 병렬화가 더 잘되기 때문에 depth를 늘리는 것보다 효율적으로 연산할 수 있다

- ResNeXt
    
    <img width="343" alt="Untitled 7" src="https://github.com/3umin/Study/assets/99728502/638a597b-6974-4d79-95f1-d3d402fa5dac">

    - Residual Block을 여러개의 Block으로 구성
    - 여러 Layer을 병렬로 연결
    - Resnet + Inception

- Stochastic Depth
    - 네트워크가 깊어질수록 Gradient Vanishing 문제가 발생
    - Train time에 Layer 일부를 identity connection으로 바꾸어 이러한 문제를 해결하도록 시도(Dropout과 유사)
    - Test time에는 모든 Layer를 정상적으로 활용하여 예측
    
- FractalNet, DenseNet(Dense block을 사용) 등등
