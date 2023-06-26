# Lecture 12

## CNN의 내부를 분석하는 방법

### First Layer : Visualize Filters

<img width="424" alt="Untitled" src="https://github.com/3umin/Study/assets/99728502/0bcbe4c1-7d87-49ad-9a2c-7db5e2d89e14">

- 첫번째 Conv Layer의 필터와 원본 이미지가 연산한 결과가 첫번째 Layer의 결과
- 첫번째 Layer는 입력이미지와 직접적으로 연산을 수행하기 때문에, 이 필터를 시각화시키는 것 만으로도 이 필터가 이미지에서 무엇을 찾는지 알아낼 수 있다.
- 어떤 이미지를 학습시키든, Conv Layer의 첫번째 층에서는 oriented edges나 보색을 찾는 등의 역할을 수행한다(실제 시각피질 연구와 동일)
- 첫번째 층 이후부터는 입력이미지와 직접적으로 연결되어 있지 않기 때문에 이미지를 보고 Intuition을 얻기는 힘들다

### Last Layer

- 마지막 Layer는 클래스 스코어를 출력
- 마지막 Layer를 시각화 할 때는 Nearest Neighbor 기법을 사용
    
    <img width="283" alt="Untitled 1" src="https://github.com/3umin/Study/assets/99728502/a0b587a2-537c-47f7-87d4-077901eb4bce">

- Lecture 2에서 했던, 이미지 공간 내의 NN을 찾아 시각화하는 방법
- 픽셀 단위의 NN이 아닌 이미지 단위의 NN이기 때문에, 2행의 코끼리의 예시를 보면 픽셀 값이 완전히 다르지만(코끼리가 왼쪽 / 오른쪽에 있는 이미지) 두 이미지는 가깝게 위치한다 → 네트워크가 Sementic 한 특징을 포착하도록 잘 학습한 결과
- 마지막 Layer에 NN을 적용하는 방법
    - 이미지를 네트워크에 통과시킨 뒤, 각 이미지에 해당하는 4096-dim 벡터들을 전부 다 저장
    - 저장된 4096-dim 벡터를 가지고 NN을 수행

- 또 다른 방법으로 “차원 축소”의 관점으로 볼 수 있다.
    - 위 방법대로 4096-dim 벡터를 저장한 뒤, 이 벡터에 t-SNE와 같은 PCA 알고리즘을 적용해 2-dim으로 압축
    - 2-dim 특징 공간의 각 Grid에 압축된 2-dim 특징을 시각화해 학습된 특징 공간의 기하학적인 모습을 시각화할 수 있음

### Middle Layer

<img width="296" alt="Untitled 2" src="https://github.com/3umin/Study/assets/99728502/59e21b11-a68f-4959-a7a6-18e298e42256">


- 중간 Layer의 가중치는 시각화해도 특징을 이해하기가 쉽지 않으나, Activation을 시각화한다면 얘기가 다르다.
- 이 특징맵을 통해서, 네트워크의 어떤 레이어는 사람의 얼굴을 찾고 있다는 사실을 알 수 있다

- 중간 특징을 시각화하는 방법 - Maximally Activating Patches
    
    <img width="197" alt="Untitled 3" src="https://github.com/3umin/Study/assets/99728502/872d4431-7152-4dd6-a45d-d3265abf33a9">

    - 어떤 이미지가 들어와야 각 뉴런의 활성이 최대화되는지를 시각화
    - 각 뉴런은 전체 이미지를 보는 것이 아니고, 이미지의 일부분만 보고 있음
    - 특정 레이어의 특징을 최대화시키는 이미지의 일부분을 시각화하는 방법
    - 각 행에 있는 패치들이 하나의 뉴런에서 나온 것. 각 뉴런별로 특징을 최대화시키는 이미지를 정렬한 것

- Maximally Activating Pathes 기법과 유사하게, 비슷한 기법 또 하나
    
    <img width="214" alt="Untitled 4" src="https://github.com/3umin/Study/assets/99728502/05610814-88b2-40dd-a711-d31e85db5455">

    오른쪽 히트맵은 어느 부분에서 요동치는지를 시각화
    
    - 입력의 어떤 부분이 분류를 결정짓는 근거가 되는지를 실험
    - 입력 이미지의 일부를 가리고, 가린 부분을 데이터셋의 평균값으로 대체
    - 가려진 이미지를 네트워크에 통과하여 확률을 추출하고, 이 가림 패치(Occluded patch)를 전체 이미지에 대해 돌아가면서 같은 과정을 반복
    - 만약 이미지의 일부를 가렸는데 네트워크의 score가 요동친다면, 그 가려진 부분이 핵심적인 부분이라는 접근법

- Saliency Maps
    
    <img width="437" alt="Untitled 5" src="https://github.com/3umin/Study/assets/99728502/3d9cb5af-f9c9-4de5-a5d5-67c0e7280221">

    - 입력 이미지의 각 픽셀들에 대해 예측한 스코어의 Gradient를 계산
    - 픽셀 단위로 어떤 픽셀이 핵심적인 특징을 가지는지를 확인
    - 위의 사진처럼, 개 이미지의 Saliency map을 만들어보면 개의 윤곽이 나타남

- Guided Back Propagation
    
    <img width="336" alt="Untitled 6" src="https://github.com/3umin/Study/assets/99728502/063b05ec-fa5c-465c-943e-ea788d5dd9fe">

    - 클래스 스코어가 아니라 네트워크의 중간 뉴런을 하나 골라서, 입력 이미지의 어떤 부분이 그 뉴런의 값에 영향을 주는지를 찾는 것
    - Saliency map과 유사하지만, 스코어에 대한 그레디언트가 아니라 입력 이미지의 각 픽셀에 대한 네트워크 중간 뉴런의 그레디언트를 계산
    - 하지만 이 Back Prop 과정에서 약간의 트릭을 주어 더 깨끗한 이미지를 가져올 수 있음(ReLU의 그레디언트 부호가 양수면 통과, 음수면 Backprop 하지 않는 방법)
    - 이로 인해 전체 네트워크가 ‘양의 부호’인 그레디언트만 이용해 학습
    - 입력 이미지에 어떤 픽셀이 해당 뉴런에 영향을 미치는지를 시각화
    - 맨 상단의 예를 살펴봤을 때, 이 뉴런은 아마 둥그런 것을 찾고 있다는 것을 짐작할 수 있다(Maximally Activating Patch를 보았을 때) → 이 짐작은 실제로 이미지 상의 둥근 부분이 뉴런의 값에 영향을 주고 있다는 사실(좌측 그림)을 통해 확정할 수 있다
    - 중간레이어가 무엇을 찾고 있는지를 시각화

- Gradient Ascent
    - 네트워크의 가중치를 모두 고정하고, 중간 뉴런 혹은 클래스 스코어를 최대화 시키는 이미지의 픽셀을 만들어냄
    - Regularization term을 추가(생성된 이미지가 특정 네트워크의 특성에 완전히 과집합되는 것을 방지)
    - Regularization을 추가하는 이유
        - 이미지가 특정 뉴런의 값을 최대화시키는 방향으로 생성되길 원함
        - 이미지가 자연스러워 보여야 한다
            
            <img width="298" alt="Untitled 7" src="https://github.com/3umin/Study/assets/99728502/ebe48111-0a59-4282-819e-ea3544c3d637">

    - 덤벨, 컵, 레몬 등등은 대강 그 특징이 보인다
    - Regularization term에 Gaussian Blur, Cut Pixels with small values to 0, Cut pixels with small gradients to 0와 같은 또 다른 규제를 추가하면 더 자연스럽게 나온다
        
        <img width="217" alt="Untitled 8" src="https://github.com/3umin/Study/assets/99728502/ffa02c38-e309-4772-bf25-fe0a851a4204">

        <img width="713" alt="Untitled 9" src="https://github.com/3umin/Study/assets/99728502/3f57c727-e808-46f5-9494-20313ecaa827">

    - 여러 제약과 모델을 추가하여 이렇게 훨씬 Intuition이 높은 이미지를 생성할 수도 있음

- Fooling Images(Adversarial Example)
    
    <img width="257" alt="Untitled 10" src="https://github.com/3umin/Study/assets/99728502/f7dd3b78-b65b-4cfd-8aee-c91679754fa4">

    - 네트워크를 속이는 이미지를 만드는 것
    1. 임의의 이미지에서 시작
    2. 임의의 가짜 클래스를 지정
    3. 가짜 클래스의 확률을 극대화하도록 이미지를 조금씩 수정
    4. 네트워크가 속을 때까지 반복
    - 사람의 눈에는 둘다 큰 차이가 없지만, 네트워크는 속는 문제가 발생…

- DeepDream
    1. 입력 이미지를 CNN의 중간 레이어에 통과시켜 activation을 계산
    2. 해당 층의 Gradient를 activation 값으로 설정
    3. Gradient를 업데이트하고, 이미지를 업데이트 하는 과정을 반복
    
    → 해당 레이어에 어떠한 특징이 있던지 그 특징을 Gradient로 설정하면 그 특징을 더욱 증폭시키는 역할을 수행
    
    <img width="485" alt="Untitled 11" src="https://github.com/3umin/Study/assets/99728502/a2c3ec9d-4390-41f3-9040-70150d92c657">

    - 평범한 하늘 사진을 바탕으로 수행했을 때 나선 모양, 동물, 건축물, 자동차 등이 나타남
    - 이러한 데이터에 Dog가 많이 나타나는 것은 이 네트워크를 학습시킨 데이터의 영향을 많이 받음
    - ImageNet 데이터로 학습을 수행했는데, 클래스 1000개 중 200개가 개여서 그렇다

- Feature Inversion
    - 이미지를 네트워크에 통과시킨 후, activation map을 저장한 뒤 이 activation map만을 가지고 이미지를 재구성
    - Regularizer를 추가한 Gradient Ascent를 이용하는데, 스코어를 최대화시키는 대신 특징 벡터간의 거리를 최소화 시키는 방법을 이용
        
        <img width="530" alt="Untitled 12" src="https://github.com/3umin/Study/assets/99728502/6d6178dc-0d3f-4999-b5ac-648fdaa520ff">

    - relu2_2의 이미지를 보았을 때, relu2_2에서는 이미지 정보를 엄청 많이 날리지는 않는다는 사실을 알 수 있음
    - 하지만 좀 더 깊게 relu5_1과 같은 이미지를 보면, 이미지의 공간적인 구조는 잘 유지하지만 디테일은 많이 죽은 것을 확인할 수 있음
    
    → 네트워크가 깊어질수록 픽셀값이 정확히 얼마인지와 같은 저수준의 정보들은 사라지고, 대신에 색이나 텍스처와 같은 미세한 변화에 더 강인한 특징에 주목한다는 사실을 알 수 있었다
    
- Texture Synthesis
    
    <img width="307" alt="Untitled 13" src="https://github.com/3umin/Study/assets/99728502/3f953d71-4c6b-4547-8ee4-c880f398dc44">

    - 작은 텍스처 패치를 입력받아서, 동일한 텍스처인 더 큰 패치를 생성하는 Task
    - NN Approch로 scan line을 따라서 한 픽셀씩 이미지를 생성하는 방법이 전통적
    - 이러한 고전적인 방법들은 간닪나 텍스처를 생성하는데에는 효과적이지만, 복잡한 텍스처에는 효과적이지 않다 → 신경망 기반 Texture Synthesis 연구 진행
    - Gram Matrix
        - 입력 사진을 네트워크에 통과시킨 후, 네트워크의 특정 레이어에서 Feature map을 가져옴( H x W grid of C-dimensional Vectors)
        - 이 Feature map을 가지고 입력 이미지의 텍스처 기술자(descriptor)을 계산
        - 두 개의 C-dimensional vector의 외적으로 C x C 행렬을 생성
        - 이 C x C 행렬은 이미지 내 서로 다른 두 지점에 있는 특징들 간의 co-occurence를 담고 있음
        - 만약 C x C 행렬의 (i, j) 번째 요소의 값이 크다는 것은 두 입력 벡터의 i번째, j번째 요소가 모두 크다는 의미
        - 이 과정을 H x W 그리드에서 전부 수행해주고, 결과를 평균내어 C x C Gram matrix를 얻는다
        → 이미지의 공간정보를 제거(평균화시켰기 때문)
        - 이 결과를 입력 이미지의 텍스처를 기술하는 텍스처 기술자로 사용
            
            <img width="532" alt="Untitled 14" src="https://github.com/3umin/Study/assets/99728502/933427e5-db8e-4ce8-a304-7d24e70461f7">

        - 이미지를 모델에 통과시키고, 다양한 레이어에서 Gram matrix를 계산한 뒤 Gradient Ascent와 유사한 과정을 거쳐 이미지를 재구성
        - 재구성된 이미지와 원본 이미지의 gram matrix간의 차이의 L2 Loss를 계산해 픽셀을 조금씩 업데이트

- Style Transfer : Feature Inversion + Texture Synthesis
    - 두개의 입력 이미지
        - Content Image : 최종 이미지가 어떻게 생겼으면 좋겠는지를 나타내는 이미지
        - Style Image : 최종 이미지의 텍스처가 어땠으면 좋겠는지를 나타내는 이미지
    - Content image의 Feature reconstruction loss도 최소화하고, Style Image의 Gram matrix loss도 최소화하는 방식으로 최적화하여 생성
        
        <img width="524" alt="Untitled 15" src="https://github.com/3umin/Study/assets/99728502/667a7e85-6ec1-4d9f-a6b4-59d3bd9b42de">

    - 이를 이용해 Style Image 화풍 스러운 Content Image를 생성할 수 있다
    - 하지만 Style Transfer는 연산 속도가 너무 느리다(수많은 Forward/Backward Pass를 거쳐야하기 때문에)
    - 해결책은 Style Transfer를 위한 모델을 다로 학습시키는 것(Content image만을 입력으로 받아서 출력하는 모델, 화풍은 고정)
    
- Style Transfer + DeepDream
    
    <img width="521" alt="Untitled 16" src="https://github.com/3umin/Study/assets/99728502/e276a257-5e38-4e6a-ab55-fafdad9341e1">

    - Content loss + style loss + deepdream loss를 최적화
    - 원하는 이미지를 특정 화풍에 맞추어서 뽑으면서, Deemdream 스러운 그림을 출력
