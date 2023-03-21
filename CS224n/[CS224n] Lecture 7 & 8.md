# [CS224n] Lecture 7 & 8

# Machine Translation(MT)

<img width="327" alt="7-1" src="https://user-images.githubusercontent.com/99728502/226569998-d772eeb6-4556-40fb-acec-3fde42c0ce26.png">

- 하나의 언어(source language)로 작성된 문장을 다른 언어(target language)로 작성된 문장 y로 변환하는 기술
- Machine Translation(기계번역)의 첫 등장은 1950년대 냉전시기
    - 소련과 미국은 항상 서로의 통신이나 기밀문서들을 자동으로 번역해서 해석하기를 원했는데, 이를 위해 처음으로 도입
    - Rule-based 방식: 두 개의 언어사전을 통해 각 단어가 대응되는 것을 찾아서 번역하는 방법
    - 사전에 대응하는 단어로 바꾸기만 하는 것이라 당연히 좋은 성능을 보여주진 못했음

- Statiscal Machine Tranlsation(SMT)
    
    <img width="374" alt="7-2" src="https://user-images.githubusercontent.com/99728502/226570027-2c2151ee-8ac4-4343-a54b-ffb34486f318.png">

    - 데이터를 통해 확률 분포를 학습하는 방법(90년대~2010년대 탄생)
    - input sentence x를 output sentence인 y로 번역할 때, 확률이 가장 높은 sentence를 선택
    - 베이즈 정리를 활용해 해당 확률을 위의 수식과 같은 형식으로 변형해 각각의 확률을 계산
    - P(x|y)는 Translation Model : 작은 단어나 구를 번역하는 역할
    - P(y)는 Language Model : 번역된 값을 바탕으로 좋은 문장을 뽑아내는 역할

## How to learn translation model P(x|y)?

<img width="314" alt="7-3" src="https://user-images.githubusercontent.com/99728502/226570048-7e20a44e-319e-46d9-b5e7-f7bccb7d82ef.png">

- 많은 양의 parallel data가 필요
- Parellel data: 같은 문장을 고대 이집트어, Demotic, 고대 그리스어 등으로 parallel하게 나열

### 정렬(Alignment)

<img width="291" alt="7-4" src="https://user-images.githubusercontent.com/99728502/226570066-039be525-f2b1-4476-a98a-bbd832896672.png">

- P(x|y)에 정렬변수 a를 추가 → P(x, a|y)

<img width="335" alt="7-5" src="https://user-images.githubusercontent.com/99728502/226570094-0f7920c7-8c2c-4be1-8fb1-a3aa3122a31d.png">

- 어떤 단어들은 대응되는 짝이 없을 수도 있음

<img width="270" alt="7-6" src="https://user-images.githubusercontent.com/99728502/226570114-dc9bc2df-3dd4-4928-ad94-db7ec3a02eb6.png">

- 어떤 단어들은 many-to-one(하나의 프랑스 단어가 여러개의 영어 단어로 번역)

<img width="340" alt="7-7" src="https://user-images.githubusercontent.com/99728502/226570138-1f82dde2-c3b5-4d6f-a502-afa3e8a8e7b3.png">

- 그 반대로 one-to-many도 가능

<img width="240" alt="7-8" src="https://user-images.githubusercontent.com/99728502/226570158-6cb05810-ede9-42a7-b4e5-96ee87727a25.png">

- many-to-many도 가능(딱 떨어지지 않게 여러개의 단어가 여러개의 단어로 번역)

### 이와같이 혼란스러운 상황에서 어떻게 잘 학습을 시킬 수 있을까?

<img width="373" alt="7-9" src="https://user-images.githubusercontent.com/99728502/226570177-52a5e1d2-6fbd-4712-b8b8-caf3da045076.png">

1. 무차별 대입
- 모든 가능한 y와 그 확률을 일일히 구함 → 비용소모 너무 큼!
1. Decoding(Heuristic 알고리즘)
- 낮은 확률을 가진 항목은 계산하지 않고 높은 확률 위주로 계산

<img width="314" alt="7-10" src="https://user-images.githubusercontent.com/99728502/226570191-25f2a2ba-2dd4-4deb-bc0d-afa46d9859a0.png">

- 위의 그림은 전체 경우의 수를 나열한 것
- 여기서 reasonably likely translation(낮은 확률 제거)을 취한 뒤에 전개

### SMT(Statiscal Machine Translation) 특징

- 좋은 성능을 내지만 복잡한 구조로 이루어짐
- 각 system은 여러 부분으로 나뉘어서 sub-system이 모여있는 형태
- 많은 feature engineering이 필요
- 추가적인 많은 자료 필요
- 사람의 손을 많이 거쳐야함

# Neural Machine Translation

<img width="470" alt="7-11" src="https://user-images.githubusercontent.com/99728502/226570215-5598fc49-ca68-4c9c-aba8-474c7009f7e3.png">

- Sequence-to-Sequence(seq2seq)이라고 불림
- Encoder RNN과 Decoder RNN 두개의 RNN을 포함
    - Encoder RNN에서 소스 문장을 마지막에 Encoding 시켜 Decoder RNN에 넘겨줌

ex)

1. 소스 문장(“he hit me with a pie”)을 Encoder RNN에 넣어 인코딩 생성
2. 인코딩 된 후 디코더 RNN으로 전달
3. Decoder RNN(조건부 언어모델)에서 인코딩에 따라 첫번째 output을 얻고 다음에 나올 단어의 확률분포 argmax를 구함
4. 다음 단계에서 디코더의 전 단계의 단어를 넣고 다시 피드백하여 단어를 맞춤
5. 목표 문장을 찾을때까지 반복작업 수행

## Many NLP tasks can be phrased as sequence-to-sequence

- 요약 (long text → short text)
- 문맥 파악 가능 (previous utterances → next utterance)
- Parsing (input text → output parse as sequence)
- 코드 생성 가능 (natural language → Python code)

## seq2seq 모델은 Conditional Language mode의 사례

<img width="361" alt="7-12" src="https://user-images.githubusercontent.com/99728502/226570258-6d34bf4d-a653-4358-bf67-2bc530a5d7af.png">

- Language Model : Decoder가 target sentence y의 다음 단어를 예측
- Conditional : 그 예측이 source sentence x의 조건부 확률을 통해 계산됨
- 주어진 x를 바탕으로 y1를 뽑아내고, 그 y1과 x를 다시 input으로 넣고 y2를 출력, 등등 반복

## 어떻게 NMT를 Train하는가?

<img width="426" alt="7-13" src="https://user-images.githubusercontent.com/99728502/226570283-e5fbe911-70af-4914-ad28-c4e46302394e.png">

- Encoder RNN에 문장을 넣고, 디코더 RNN의 모든 단계에서 각각 다음에 올 y hat의 손실을 계산
- 손실을 모두 더한 뒤 단계 수로 나누어 최종 loss 값 계산
- Backpropagation은 전체 시스템에 걸쳐 진행. 시스템을 전체적으로 최적화하는 것이 학습 중점
- end-to-end 방식으로 문장 요소를 모두 고려해 좋은 결과물 도출

## Multi-layer RNN을 통해 더 복잡한 표현을 계산할 수 있음

<img width="484" alt="7-14" src="https://user-images.githubusercontent.com/99728502/226570305-bd699bf8-e63c-4a1e-bd58-5c8a5f34ca6c.png">

- lower RNN은 lower-level feature을 계산하고, higher RNN은 higer-level feature을 계산
    
    ⇒ High-performing RNN은 보통 Multi-layer 형태
    
- 보통 2 ~ 4개의 layer가 Encoder RNN에 적합하고, Decoder RNN에는 4개의 layer가 적당

## Greedy decoding

<img width="188" alt="7-15" src="https://user-images.githubusercontent.com/99728502/226570329-dcccf5c2-ed6f-47fe-b3d2-e746b39b9cce.png">

- 가장 확률이 높은 단어를 도출하다 보니 한번 내린 결정을 수정 못함
- 만약 ‘he hit me with a pie’가 정답인데, hit 다음에 me가 아니라 a가 나오게 된다면, 틀린 문장임을 알더라도 다시 돌아가서 a를 me로 수정할 수 없고, 이미 나와버린 a 다음으로 계속해서 다음 단어들을 출력해야함

### Greedy decoding의 해결책

1. Exhuastive search decoding

<img width="355" alt="7-16" src="https://user-images.githubusercontent.com/99728502/226570350-a1e0214b-420d-4355-80f2-ae580e813b98.png">

- 철저하게 가능한 sequence y에 대해서 계산
- 디코더의 각 단계 t에서, $V^t$개의 가능한 부분 번역을 모두 계산(V: 단어 크기)
- 이렇게 되면 계산하는데 드는 비용이 너무 큼 → Beem search decoding

1. Beem search decoding

<img width="367" alt="7-17" src="https://user-images.githubusercontent.com/99728502/226570366-6dee0d0a-121f-4d27-8fe3-a8e2d0bfc9c3.png">

- 디코더의 각 단계에서 k 개의 가장 가능성이 높은 부분 번역을 선택해 진행
- k는 빔의 크기가 되고, k의 크기를 증가시킬수록 더 많은 것들을 고려하게 됨
- 가설 y1, ... , yt는 log probability로 표현되는 score가 존재
- k 개의 가설의 점수가 가장 높은 번역을 찾는게 핵심
- 최적의 방법이라고 보장할 수는 없지만, Exhuastive search decoding보다는 훨씬 효율적

### Beem search decoding 예시

<img width="423" alt="7-18" src="https://user-images.githubusercontent.com/99728502/226570384-f7f35f32-5384-4093-80c7-4aba0d4c1e58.png">

- k=2이며 he와 i에서 시작함
- he와 i 뒤에 score가 높은 것들을 각각 2개씩 구하고, 그 2개씩 뽑은 것들에서 score가 높은것들을 2개씩 계속 뽑으며 진행
- 최종적으로 가장 확률이 높은 줄기인 ‘he hit me with a pie’가 나옴

- Greedy decoding에선 모델이 <END> 토큰을 출력할 때까지 반복진행
- 각 가설마다 <END> 토큰이 출력되는 timestep은 다를 수 있음
- 보통 미리 지정한 한계 타임스텝에 도달하거나, 미리 지정한 가설의 수만큼 완성하면 종료
- Beem search decoding은 한 가설이 end 토큰을 생성하며 끝이 나면, 그것은 다로 두고 다시 다른 가설을 탐색 ⇒ 다시 돌아올 수 없어서 오류를 수정할 수 없는 문제 해결

<img width="368" alt="7-19" src="https://user-images.githubusercontent.com/99728502/226570408-1422368f-9a27-4169-8070-0037d1876921.png">

<img width="183" alt="7-20" src="https://user-images.githubusercontent.com/99728502/226570426-e79e25fc-0358-4fd6-a10a-fb6ba9bdcf23.png">

- Beem search decoding을 거쳐 완성된 가설 중 가장 높은 점수를 받은 것을 선택
- 여기서 문제는 가설의 길이가 길수록 낮은 점수를 받게 되어서 정확하지 않더라도 짧은 길이의 가설을 채택하는 경우가 생김 ⇒ time step의 수(t)로 나누어 평균을 통해 이 문제 해결

## Advantages, and Disadvantages of NMT

- 장점
    - 더 나은 성능
    - 하부구조가 개별적으로 optimized 되는 것이 아닌 전체적으로 end-to-end 방식을 통해 optimize가 이루어져서, 간단하고 편리
    - 인간의 노력 덜 필요

- 단점
    - 해석하기가 어려움(디버그 어려움)
    - 통제 어려움(번역에 대한 가이드라인이나 규칙을 쉽게 정의할 수 없음)
    

## Machine Translation의 평가

- BLEU(BiLingual Evaluation Understudy)
    - machine-written translation과 하나 또는 여러개의 human-written translation과의 유사성을 비교

- MT progress over time

<img width="514" alt="7-21" src="https://user-images.githubusercontent.com/99728502/226570466-0271d399-5b9a-42bb-a9ee-10957dd5531c.png">

- 2014년에 첫번째 seq2seq 논문이 나오고, 2016년에 구글번역이 SMT에서 NMT로 전환
- 지난 수년간 수백명의 엔지니어가 이뤄내던 성과를 소수의 엔지니어로 몇달만에 더 나은 성과를 보였음

NMT의 개발로 많은 점이 좋아졌지만, 한계점도 여전히 존재

- 목표어휘에 없는 단어 생성 불가
- train data와 test data간의 도메인 불일치
- 긴 텍스트에 대한 문맥 유지 어려움
- 리소스가 부족한 언어쌍
- 문장의 의미를 정확하게 파악하기 힘듬
- 대명사 정의 오류
- 형태적 오류
    
    ⇒ 이를 해결할 Attention 기법 탄생
    

# Attention

## Sequence-to-sequence: bottelneck problem

<img width="431" alt="7-22" src="https://user-images.githubusercontent.com/99728502/226570503-2574f3a2-0e0b-468c-8b87-73342dae64be.png">

- Encoder RNN의 마지막 단계에서 문장 전체의 인코딩 진행 → 정보가 쏠려버리는 병목현상 발생

 

## Attention : 각 단계별로 집중도를 주어 병목현상 해결

<img width="419" alt="7-24" src="https://user-images.githubusercontent.com/99728502/226570526-c6b72d9d-f5c2-4918-ada2-025b42a2dd0d.png">

- 디코더와 인코더의 각 단계별로 내적을 시켜 Attention score을 구함
    - Attention score : 인코더의 모든 hidden state 각각의 항목이 디코더 현 시점의 hidden states와 얼마나 유사한지 보여줌
- 4개의 Attention score을 소프트맥스에 넣어 Attention distribution을 구함
- Attention distribution과 인코더의 hidden states를 가중합하여 Attention output을 뽑고, Attention ouput과 디코더의 hidden state를 결합하여 최종적으로 yhat 도출

### Attention 수식화

<img width="491" alt="8-1" src="https://user-images.githubusercontent.com/99728502/226570568-1439b6fa-7bb8-465c-8aef-57bdfd977bf1.png">

- Encoder hidden states와 Decoder hidden states의 내적으로 Attention scores를 계산
- Attention scores를 softmax에 집어넣어 Attention distribution을 구함
- Attention distribution과 Encoder hidden states의 가중합을 통해 attention output 계산
- Attention output과 Decoder hidden state와 결합하여 최종적으로 y hat을 구함

### Attention is great!

- NMT 성능을 향상시킴
- MT 과정에서 더 ‘사람과 비슷한’ 방법을 제공
- bottleneck problem(병목현상) 해결
- Vanishing Gradient problem 해결
- 해석 가능성 제공

### Attention variants 계산방법

<img width="520" alt="8-2" src="https://user-images.githubusercontent.com/99728502/226570580-492764e4-e3f0-4cf6-8818-7875ff84b941.png">

- Multiplicative attention
    - Attention score을 계산할 때 일부 정보만 가져다가 계산
- Reduced-rank multiplicative attention
    - W matrix가 너무 많은 파라미터를 보유하고 있어서 이를 줄이기 위함
    - W = $U^TV$
- Additive attention : original version
