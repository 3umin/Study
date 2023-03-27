# Lecture 5

# Neural Network

### Mark 1 Perceptron Machine(1957)

<img width="233" alt="Untitled" src="https://user-images.githubusercontent.com/99728502/227854349-1898f1c0-6600-40e5-9878-18e5f4599283.png">

- 1957년 Frank Rosenblatt가 개발
- 퍼셉트론을 구현한 최초의 기계
- 가중치 W를 Update하는 Rule이 존재(Backprop과 유사하지만, 당시에는 Backprop이란 개념이 없어서 단지 W를 이리저리 조절하면서 맞추는 방식)

### Adaline/Madaline(1960)

<img width="622" alt="Untitled 1" src="https://user-images.githubusercontent.com/99728502/227854367-9faa0856-4bce-4755-a034-d148c1a0a942.png">

- Widrow and Hoff가 개발
- 최초의 Multilayer Perceptron Network
- 이 시점에서 비로소 Neural Network와 비슷한 모양
- 하지만 여전히 Backprop같은 알고리즘은 없음

### Backprop(1986)

<img width="612" alt="Untitled 2" src="https://user-images.githubusercontent.com/99728502/227854385-c41b1db0-7045-4144-8f67-b9414f2b1c21.png">

- Rumelhart가 제안
- Chain rule, Update rule 사용
- 최초로 Network를 학습시키는 것에 대한 개념이 정의

### Reinvigorated research in Deep Learning(2006)

- DNN의 학습가능성과 효과성을 입증
- 여전히 모던한 NN은 아님(세심한 초기화 불가)
- 전처리 과정이 필요했고, 초기화를 위해 RBM을 이용해 각 히든레이어의 가중치를 학습시킴
- 이 과정을 통해 초기화된 Hidden layer을 이용해서 전체 신경망을 Backprop 하거나 Fine tune 진행

### 2012년부터 NN 광풍!

- 음성인식, 영상인식, ImageNet 등등에 활용
- CNN도 이때부터 널리 활용

<img width="429" alt="Untitled 3" src="https://user-images.githubusercontent.com/99728502/227854401-354f9b4f-f33b-465a-8b34-21f096421dc0.png">

- Hubel & Wiesel - 일차시각피질의 뉴런에 대한 연구
    - 피질 내 서로 인접한 세포들은 Visual Field내에 어떤 지역성을 가짐
    - 피질 내부에 지형적인 매핑이 있다는 사실을 찾아냄
        
        <img width="239" alt="Untitled 4" src="https://user-images.githubusercontent.com/99728502/227854422-6fc9f6f9-a087-4744-bc83-03127d895d78.png">

    - 뉴런들이 계층적인 구조를 이룸
        
        <img width="373" alt="Untitled 5" src="https://user-images.githubusercontent.com/99728502/227854440-a1654bdc-529c-4f7a-8a33-67d051c44e49.png">


- Neocognitron(1980)
    
    <img width="586" alt="Untitled 6" src="https://user-images.githubusercontent.com/99728502/227854449-2917c511-b610-4fb6-8571-b979f6fa1efb.png">

    - Hubel과 Wiesel이 발견한 Simple/Complex cells의 아이디어를 사용한 최초의 NN
    - Simple cells는 학습 가능한 parameter을 가지고 있음
    - Compelx cells는 pooling과 같은 것으로 구현했는데 작은 변화에 Simple cells보다 좀 더 강인
    - 이러한 Simple Cell과 Complex Cell을 교차하여 배치

- Gradient-based learning applied to document recognition(1998)
    
    <img width="429" alt="Untitled 7" src="https://user-images.githubusercontent.com/99728502/227854474-d8f4efd3-fa04-4203-88ff-ebdb804f2c86.png">

    - 최초로 NN을 학습시키기 위해 Backprop과 Gradient-based learning을 적용
    - 문서인식에 아주 잘 동작
    - 특히 우편번호의 숫자를 인식하는데 아주 잘 동작 → 실제 우편번호 인식에 사용됨

- AlexNet(2012)
    
    <img width="373" alt="Untitled 8" src="https://user-images.githubusercontent.com/99728502/227854483-30194b7c-8a6f-42e5-9ecc-1afdf754bcb1.png">

    - CNN의 현대화 바람
    - 이전의 CNN보다 더 크고 깊어짐
    - 가장 중요한 점은 ImageNet dataset과 같이 대규모의 데이터를 활용할 수 있음(GPU의 발전 등으로 인해)

- 현재 ConvNets는 이미지 분류, 검색, Detection, Segmentation, 자율주행, 얼굴 인식, 비디오 분석, pose recognition, 강화학습, 의학 진단, Image Captioning(이미지에 대한 설명을 만들어내는 것), 생성모델(Deep Dream) 등에 널리 사용

# Convolutional Neural Networks

### Fully Connected Layer(FC Layer)

<img width="593" alt="Untitled 9" src="https://user-images.githubusercontent.com/99728502/227854519-4aaa2be2-7697-4f73-ad5a-12dd7a88ee42.png">

- 어떤 벡터를 가지고 연산을 하는 것
- 예를 들어 32 x 32 x 3 사이즈의 이미지를 길게 펴서 3072차원의 벡터로 만들고, 가중치 W와 곱하여 Activation을 얻음

## Convolutional Layer

<img width="560" alt="Untitled 10" src="https://user-images.githubusercontent.com/99728502/227854530-293f9dd9-343e-49f9-9c03-4fa52858e51f.png">

- Convolutional Layer은 FC Layer과 다르게 기존의 이미지 구조를 그대로 유지하게 됨
- 작은 필터가 가중치의 역할을 하게 되고, 필터가 이미지를 슬라이딩 하면서 공간적으로 내적을 수행
- 필터는 입력의 깊이(Depth)만큼 확장
- 필터 이미지의 어떤 공간에 겹쳐놓고 내적을 수행
    - 내적을 할 때는 FC처럼 5x5x3 사이즈의 필터가 75차원의 벡터로 길게 펴져 진행

### 어떻게 Sliding을 하는가?

<img width="596" alt="Untitled 11" src="https://user-images.githubusercontent.com/99728502/227854541-71042170-dbb3-4269-a88b-f81aff239224.png">

- 이미지의 좌상단부터 시작
- 필터의 모든 요소를 가지고 내적을 하게 되면 하나의 값이 나오고, Conv 연산이 수행된 값들을 Output activation map의 해당하는 위치에 저장하게 됨
    - 출력행렬의 크기는 슬라이드를 어떻게 하느냐에 따라 달라짐
- 하나의 필터를 가지고 전체 이미지에 Convolution 연산을 수행하는데, 보통 Conv Layer에는 여러개의 필터를 사용(필터마다 다른 특징을 추출하기 때문)
    - 여기선 필터의 depth를 보고 필터가 여러개라고 하는 것이 아님(필터는 항상 이미지의 depth와 동일하며, 이 필터를 한 개로 봄)
    - 여러개의 필터를 통과하면 다음 그림과 같은 Conv Layer의 연속된 형태가 됨
    - 각각을 쌓아올리면 간단한 Linear layer로 된 NN이 되며, 그 사이사이에 activation function을 넣어 학습 진행
    
- Layer들은 계층적으로 학습을 진행함(저수준 특징부터 고수준 특징 순서대로)
    
    <img width="601" alt="Untitled 12" src="https://user-images.githubusercontent.com/99728502/227854556-fa4ce0c7-38b3-42b0-a5be-44113cf24250.png">

- 필터의 예시
    
    <img width="601" alt="Untitled 13" src="https://user-images.githubusercontent.com/99728502/227854630-82bdfbcc-7320-42a6-af79-cd4d87e33ac2.png">

    - 각 activation은 이미지가 필터를 통과한 값이 되며, 이미지 중 어느 위치에서 이 필터가 크게 반응하는지를 보여줌

- Convolutional 신경망을 거치면서 변하는 이미지의 예시
    
    <img width="629" alt="Untitled 14" src="https://user-images.githubusercontent.com/99728502/227854644-c604797b-ec93-496d-a2f1-e240a77db923.png">


### Spatial Dimension

<img width="443" alt="Untitled 15" src="https://user-images.githubusercontent.com/99728502/227854669-ba5307c0-59aa-4fc0-aec6-fc3e7a10aef2.png">

- 좌측 상단부터 필터를 씌워 해당 값들의 내적을 수행(이 값은 activation map의 좌상단에 위치)
    - 이후 오른쪽으로 한칸을 움직여 다시 내적(stride = 1인 상태)
    - 이렇게 되면 output은 5x5 크기가 나옴
- 만약 슬라이딩을 2칸씩(stride = 2) 움직인다면, output 값은 3x3 크기가 나옴
- 만약 stride = 3이라면, 이미지를 슬라이딩해도 필터가 모든 이미지를 커버할 수 없음 → 이 경우 불균형한 결과가 나와 잘 동작하지 않음
- 상황에 따라 출력의 사이즈가 어떻게 될 것인지를 계산하는 식(N : 입력차원, F : 필터사이즈)
    
    $output \ size = (N-F)/stride + 1$
    
    - 이를 이용해 stride를 어떤 값으로 사용해야 이미지에 꼭 맞는지, 그리고 몇 개의 출력값을 낼 수 있는지 알 수 있음

### Zero-pad

<img width="611" alt="Untitled 16" src="https://user-images.githubusercontent.com/99728502/227854685-77498058-008c-47d0-9f62-2fd86e0c8646.png">

- 출력 사이즈를 의도대로 만들어주기 위함
- 또한 가장자리에 있는 데이터가 잘 반영되도록 사용하기도 함
- 이미지의 가장자리에 0을 채워넣어 이미지를 키우는 것
- 7 x 7 입력에 stride=1인 3 x 3 필터 연산을 수행할 때 1칸의 zero-padding을 한다면 (7+ 1x2 -3)/1 +1 = 7x7 사이즈의 output이 출력됨

- 파라미터의 수는 Filter의 사이즈와 연관
    - 5x5x3 사이즈의 필터가 10개가 존재한다면, 가중치로 들어가는 파라미터들이 5x5x5x10 = 750개 존재
    - 또한 필터 당 하나의 편향값이 존재하므로 1 x 10 = 10개의 편향값 존재
    - 총합 760개의 파라미터

### Brain/neuron view of Conv Layer

<img width="614" alt="Untitled 17" src="https://user-images.githubusercontent.com/99728502/227854697-9755b51f-9a4a-4e23-a154-c4c376a4f3ca.png">

- 이미지의 일부분이 Filter를 통과하며 1개의 숫자값이 출력됨
- 기본적인 뉴런과 다른 점은, local connectivity가 존재한다는 점.
- 하나의 뉴런은 한 부분만 처리하고, 그런 뉴런들이 모여서 전체 이미지를 처리
- 5x5 필터 → 5 x 5 Receptive filed for each neuron
    - Receptive field : 한 뉴런이 한 번에 수용할 수 있는 영역
- 5개의 필터로 통과된 3D Grid가 있을 때, 이 Grid의 한 지점에서 depth 방향으로 바라본다면 이 5개의 점은 정확하게 같은 지역에서 추출된 서로다른 특징임
- 즉 같은 지역을 돌더라도 서로다른 특징을 추출

### Pooling Layer

- Representation들을 더 작고 관리하기 쉽게 해줌
- Representation을 작게 만드는 이유? → 작아질수록 파라미터의 수가 줄어들기 때문, 또한 일종의 공간적인 불변성을 얻을 수 있음
- Pooling Layer은 Down Sampling의 기능을 수행(Depth에는 아무영향 없음)

- Max Pooling
    
    <img width="546" alt="Untitled 18" src="https://user-images.githubusercontent.com/99728502/227854713-4126415b-8218-4081-9da3-0d191f59795e.png">

    - 필터 크기를 지정할 수 있음
    - 여기서 필터 크기는 얼마만큼의 영역을 한 번에 묶을지를 지정
    - Conv Layer처럼 필터를 통과하며 계산하는데, Conv Layer과 다르게 내적을 하지 않고, 필터 내의 최댓값만 Output으로 출력
    - Pooling 할 때는 겹치지 않는 것이 일반적이며, Padding을 사용하지 않음
