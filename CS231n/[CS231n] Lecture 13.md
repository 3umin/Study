# Lecture 13

- 지도 학습 : Label 값이 주어진 Task
- **비지도 학습** : Label 값이 주어지지 않은 Task

## Generative Model(생성 모델)

<img width="383" alt="Untitled" src="https://github.com/3umin/Study/assets/99728502/34f9d443-2b36-4cd6-8cbe-b552bcbdb3dd">

- 비지도 학습의 일종으로, 동일한 분포에서 새로운 샘플을 생성하는 모델
- 생성 모델이 중요한 이유?
    - super resolution(화질 개선), colorization(자동 색칠) 등에 활용할 수 있음
    - 시계열 데이터에 생성 모델을 적용하여 시뮬레이션과 계획에 사용할 수 있음
- 생성 모델의 종류
- 
    <img width="512" alt="Untitled 1" src="https://github.com/3umin/Study/assets/99728502/3a249e99-425b-4d15-9041-ffa17dcda995">

    

## PixelRNN/CNN

<img width="451" alt="Untitled 2" src="https://github.com/3umin/Study/assets/99728502/7cfa9439-3261-4762-949f-58300e586793">

- Density를 명시적으로 정의하고 모델링
- 이미지 x에 대한 likelihood인 p(x)를 chain rule을 이용해 1차원 분포들간의 곱으로 분해
- 모델을 학습시키려면 학습데이터의 likelihood를 최대화하면 되는데, 픽셀 값에 대한 분포는 복잡함
- 이미지 내 픽셀들의 분포를 알아내는 법? → 신경망

### PixelRNN

<img width="496" alt="Untitled 3" src="https://github.com/3umin/Study/assets/99728502/b6e24e1b-7061-4059-8fd5-87c3f9ea2581">

- 좌상단 코너부터 시작해 화살표 방향으로의 연결성을 기반으로 순차적으로 픽셀을 생성
- 방향성을 기반으로 픽셀들간의 dependency를 RNN(LSTM)을 이용하여 모델링
- 순차적인 생성 방식이기 때문에 매우 느리다는 단점

### PixelCNN

<img width="499" alt="Untitled 4" src="https://github.com/3umin/Study/assets/99728502/a42c7f20-4646-4927-8818-56f49ede662a">

- 왼쪽 코너에서부터 이미지를 생성
- 모든 종속성을 고려하여 모델링하는 RNN과 다르게, PixelCNN은 Context Region 안에 들어오는 픽셀만 CNN으로 모델링
- CNN을 수행하면 각 픽셀 값을 알고있기 때문에 Train time에 병렬 연산이 가능하다.
- 하지만 Test time에서는 데이터를 모르기 때문에 코너에서부터 시작 → 여전히 느리다

- 요약하자면, PixelRNN/CNN은 likelihood p(x)를 명시적으로 계산하는 방법
- 하지만 생성 과정이 순차적이기 때문에 상당히 느리다

# VAE(Variational AutoEncoder)

<img width="482" alt="Untitled 5" src="https://github.com/3umin/Study/assets/99728502/1eba16a9-11bf-4cc5-bfba-14fc594d001c">

- 계산이 가능한 확률모델을 기반으로 하는 PixelRNN/CNN과 다르게, VAE는 직접 계산이 불가능한 확률 모델을 정의.
- 추가적인 잠재변수 z를 모델링
- 이 식을 직접 최적화시킬 수는 없지만, likelihood의 lower bound를 구해서 최적화 시킴

## AutoEncoder

<img width="421" alt="Untitled 6" src="https://github.com/3umin/Study/assets/99728502/2fefcd58-e009-4553-96a8-b5da7181e795">

- AE는 레이블되지 않은 학습데이터로부터 저차원의 feature representation을 학습하기 위한 비지도 학습
- Input data X를 특징 Z로 Encoder(Neural Net)가 매핑시킴
- 일반적으로 z는 x보다 작기 때문에, AE를 통해 차원축소의 효과를 기대할 수 있다
- AE는 원본을 다시 복원하는데 사용될 수 있는 특징들을 학습하는 방식
    - Encoder가 x를 더 낮은 차원의 z로 매핑시키고, decoder에 넣어 x와 유사한 데이터로 복원
    - 일반적으로 Encoder와 Decoder는 대칭적으로 설계
    - Encoder는 고차원(x)를 저차원(z)로 보내기 때문에 Convnet으로
    - Decoder는 저차원(z)를 고차원(x)로 보내기 때문에 Upconvnet으로 설계
    - Decoder가 복원한 이미지와 원본 이미지와의 차이를 계산하며 학습 수행
    
    <img width="419" alt="Untitled 7" src="https://github.com/3umin/Study/assets/99728502/26b6e812-177f-4a0e-9628-e46d57933b44">

- 학습이 끝나면 Decoder을 버리고, Encoder가 학습한 feature mapping을 지도학습 모델의 초기 가중치로 사용할 수 있다
- Decoder를 버리고 위에 분류기를 붙이기만 하면 분류기로 사용 가능

## VAE

- 학습 데이터 $x_i$가 관측할 수 없는 잠재변수 z에 의해 생성된다고 가정
- VAE는 AE와 다르게 Z를 이용해 X를 생성
- z에 대한 prior로부터 샘플링을 수행
    - prior : 확률분포 관점에서 어떠한 event가 일어날지에 대한 기댓값
- True parameter $\theta^*$(Prior distribution, Conditional distribution)를 추정.
    - prior은 gaussian과 같은 단순한 모델을 선택하는 반면, Conditional distribution은 좀 더 복잡한 함수인 neural network로 모델링

- decoder는 잠재변수 z를 입력받아 원본 이미지와 비슷한 이미지로 디코딩하는 역할을 수행
- $p_{\theta}(x) = \int p_\theta (z)p_\theta (x|z) dz$
- 위 likelihood 식은 계산할 수 없다
    - $p_\theta(z)$ : Simple Gaussian prior
    - $p_\theta(x|z)$ : Decoder neural network
    - 모든 z에 대한 적분이 불가…
    - 이를 위해 decoder network 뿐 아니라 추가적인 encoder network를 정의 ($q(z|x)$)
    - 이를 이용해 data likelihood의 lower bound를 구할 수 있다

<img width="473" alt="Untitled 8" src="https://github.com/3umin/Study/assets/99728502/3579cf97-059b-4b04-9e7c-3f9a833faa8f">

1. Encoder: x를 입력으로 받아 z space 상에서 확률 분포를 만든다 (가우시안)
2. data dependent gaussian 분포로부터 z를 샘플링한다
3. 샘플링된 z를 decoder의 입력으로 넣어 x space 상의 gaussian or Bernoulli distribution을 얻는다.
4. 이렇게 얻은 분포에서 x를 샘플링한다.

- VAE를 학습할 때 사용할 loss (log likelihood를 극대)
    
    <img width="527" alt="Untitled 9" src="https://github.com/3umin/Study/assets/99728502/8d84a8e2-9cca-4759-9ccc-9677b5c7253c">

    - Decoder Network term: Reconstruction
    
    → Original input data와 유사하게 이미지를 재구성하는 역할($E_z[logp_\theta (x^{(i)}|z)]$)
    
    - 첫번째 KL term($D_{KL}(q_\phi (z|x^{(i)})||p_\theta(z))$)
    
    → 근사된 posterior의 분포가 얼마나 normal distribution과 가까운지에 대한 척도
    
    - 두번째 KL term: 무조건 0보다 크다
    - 첫번째, 두번째 항은 모두 계산이 가능하고 마지막 항은 무조건 0보다 크거나 같기 때문에 VAE likelihood의 lower bound를 계산할 수 있다.
    - 첫번째 항이 크다는 것은 데이터를 잘 복원해낸다는 의미
    - 두번째 항은 우리가 근사시킨 분포와 prior 분포와의 거리가 가까워야 좋음(가까울수록 0에 가까우니 전체 항은 커지게 됨)
    - 이렇게 계산한 lower bound가 **최대화**되도록 최적화시키면 된다

<img width="528" alt="Untitled 10" src="https://github.com/3umin/Study/assets/99728502/45a37243-1d8e-4c18-aa04-a1b2f61cbd03">

- 입력데이터를 encoder에 통과시켜 q를 얻음
- q를 구했으면 이 분포로부터 잠재변수 z를 샘플링
- 샘플링한 z를 decoder에 통과시켜 decoder의 출력 분포를 구하고, 이 분포로부터 샘플링

- VAE를 학습시키고 난 뒤에는, Decoder Network만 필요
- Posterior가 아니라 Prior에서 샘플링하여 데이터를 생성
    
    <img width="353" alt="Untitled 11" src="https://github.com/3umin/Study/assets/99728502/0317f780-eed0-4874-8966-f7f933745bf6">

- Z1 Z2의 분포에 따른 그림
- 위로 갈수록 찡그리고, 아래로 갈수록 웃고있음 → z1은 웃음의 정도를 나타냄
- 좌우에 다라 머리의 위치가 변함 → Z2는 머리 각도를 나타냄

- 일반적으로 VAE가 이미지를 잘 생성하지만 가장 큰 단점 → 이미지가 원본에 비해서 blurry 하다는 점.
- 만약 확률분포를 명시적으로 정의하는 것을 포기한다면? → GAN

## GAN

<img width="419" alt="Untitled 12" src="https://github.com/3umin/Study/assets/99728502/fbe39047-71f2-41e8-869e-094269f2bff4">

- 2 - player game 이라는 방식으로 분포를 학습
- 단순한 분포에서 우리가 원하는 분포로 변환하기 위한 함수를 학습
- Generator : 사실적인 이미지를 생성하여 Discriminator를 속이는 것이 목표
- Discriminator : 입력 이미지가 실제인지 거짓인지를 구별
- 더 잘 속이기 위한 vs 더 잘 구별하기 위한 모델간의 싸움
    
    <img width="473" alt="Untitled 13" src="https://github.com/3umin/Study/assets/99728502/c985a9ae-7f89-4a7c-b556-3b1a3cef79a2">

- 두 모델을 Min Max 목적함수를 통해 한번에 학습시킴
- discriminator는 objective function을 최대화하는 것이 목적(D(x)가 1에 가까우며(실제 데이터는 Real로 탐지), D(G(z))가 0에 가까움(가짜 데이터는 Fake로 탐지))
- generator는 objective function을 최소화하는 것이 목적(D(G(z))가 1에 가까움 → 가짜 데이터를 항상 구별)

<img width="524" alt="Untitled 14" src="https://github.com/3umin/Study/assets/99728502/c8f2b550-73a2-4dfb-939f-1899da2e4fff">

- 데이터를 학습시킬 때는 Gradient ascent와 descent를 반복하여 진행
- 하지만 실전에는, generator의 objective function이 학습이 잘 되지 않는다
    - generator가 discriminator를 잘 속인다면 gradient가 점점 더 커짐
    - 생성된 샘플이 좋지 않을 때는 discriminator가 쉽게 구별할 수 있으니까 gradient도 평평함
    - 즉 샘플이 안좋을 때(그레디언트가 가파를 때) 더 학습을 해야하는데 실제론 그렇지 않아 학습이 제대로 되지 않는다
    - 학습 능력을 향상시키기 위해서는 objective function을 변환하여 이를 해결
    - $max_{\theta_g} E_{z \sim p(z)} log(D_{\theta_d} (G_{\theta_g}(z)))$
    
- GAN를 학습시키는 방법

<img width="378" alt="Untitled 15" src="https://github.com/3umin/Study/assets/99728502/6660afbc-958f-4201-9795-00b0771a803e">

1. k번 만큼 discriminator를 학습시킴
    - noise prior Z에서 미니배치만큼 노이즈를 샘플링
    - 학습 데이터 x에서 실제 샘플을 미니배치만큼 샘플링
    - 샘플링한 노이즈를 generator에 통과시켜 가짜 이미지를 생성
    - discriminator 파라미터를 k step만큼 업데이트
2. Generator를 학습
    - 노이즈를 미니배치만큼 샘플링
    - 샘플링된 노이즈를 generator에 통과시키고 generator를 최적화시킴
