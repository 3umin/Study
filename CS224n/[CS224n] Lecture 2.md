# [CS224n] Lecture 2

## Word2Vec

Word2Vec을 생성하는 2가지 방법

- Skip-Grams(SG) : 주어진 center word(c)로 context word(o)를 예측하는 방식
    
                                → 더 자연스러운 방법, 주로 많이 사용
    
- Continuous Bag of Words(CBOW) : 모든 context word(o)로 center word(c)를 예측하는 방식

CBOW는 중심 단어를 예측하기 위해 나이브 베이즈 모델처럼 주변 단어들의 확률 정보를 

독립적으로 계산

## 최적화(Optimization)

- Gradient Descent

![img1](https://user-images.githubusercontent.com/99728502/226261587-bc7a1816-77d3-4f88-a4e4-87d103997b1a.png)

손실함수의 gradient(기울기)를 계산한뒤, 기울기의 반대방향으로 조금씩 이동 

→ 계속해서 기울기 반대방향으로 이동하다보면 최종적으로 손실함수의 최소점에 도달

$θ^{new} = θ^{old} - \alpha\nabla_θJ(θ)$

$\alpha$ : step size or learning rate

$\nabla_\theta J(\theta)$ : 손실함수의 기울기, 전체 parameter에 대해서 기울기를 일일히 계산하는 것은 비용↑ 

           → Stochastic Gradient Descent 방법 사용

## Word2vec의 효율성을 높이는 법

### Stochastic Gradient Descent(SGD)

- 전체 데이터중 일부 sample(표본)에 대해서만 기울기(gradient)를 계산해서 모수를 업데이트
- 결국 점진적으로 최소점으로 이동
- 상대적으로 노이즈가 크다

### Negative Sampling

SG(Skip-Grams)가 풀던 다중 클래스 분류 문제를 이진 로지스틱 회귀로 바꾼 것

![cs224n](https://user-images.githubusercontent.com/99728502/226261627-d1dccafe-0206-4646-ae51-76cf427a7337.png)

$-\log\sigma(u_0^Tv_c)$ : 주변 단어(U0)와 중심단어(Vc)의 내적 + sigmoid + log ⇒ 가능한 크게 유지

$\sum_k \log\sigma(-u_k^Tv_c)$ : 랜덤으로 선택된 k개의 주변단어와 중심단어 사이의 내적 ⇒ 가능한 작게 유지 

## Co-occurrence matrix(동시등장 행렬)

- Window based matrix : 한 윈도우를 기준으로 빈도 체크, 단어간 유사도 찾기 수월
- Word-document matrtix : 한 문서를 기준으로 빈도 체크, 문서의 전반적인 주제 찾기 수월

![cs224n1](https://user-images.githubusercontent.com/99728502/226261655-ebbae1db-2e13-4ec2-b850-97868e54625a.png)

- Window based co-occurrence matrix(동시등장 행렬)

각 문장에 등장하는 단어를 행과 열로 나열하고, 해당 단어별로 동시에 등장하는 단어의 빈도를 계산

→ 전체정보를 잘 포착하지만 너무 Sparse하다

→ 단어의 개수가 증가할 수록 차원이 폭발적으로 증가

- Function words(the, he, has) 등이 빈번히 등장해 분석의 왜곡 → 값 조정 필요
    1.  log, min(X, t), function words 무시 등으로 조정
    2. 같은 윈도우 안의 주변 단어에서도 중요도를 차등적으로 부여
    3. 단순집계대신 피어슨 상관관계를 사용하고, 음수 값은 0으로 설정

## GloVe(Global Vectors for Word Representation)

1. Count based(LSA, HAL, COALS, PCA 등)
    - Training 속도가 빠름
    - 통계 정보를 효율적으로 활용
    - 단어의 유사성을 계산하는데만 사용(단어 사이의 관계 확인 X)
2. Direct prediction(NNLM, HLBL, RNN, Skip-gram/CBOW 등)
    - 전체적인 통계정보를 활용하지 않음
    - 대부분의 분야에서 좋은 성능을 내는 모델 생성 가능
    - 단어 유사성에 대해 복잡한 패턴도 잡아냄
        
        ⇒ 각 기법의 장점만을 갖춘 방법인 GloVe 탄생
        
    - 임베딩된 단어벡터간 유사도 측정을 수월하게(Word2Vec)
    - Corpus 전체의 통계정보를 반영(co-occurrence matrix)
   
![cs224n2](https://user-images.githubusercontent.com/99728502/226261680-c699bda5-806f-407d-bd46-522e12f96cb4.png)

 동시등장 행렬을 바탕으로 계산,

 두 단어(ice, steam)과 모두 관련이 있는 단어나 모두 관련이 없는 단어는 값이 1에 가깝고, 

 한 단어만 관련이 있을 경우 1에서 멀어짐
 
![cs224n3](https://user-images.githubusercontent.com/99728502/226261697-dc657b88-fdbf-4c37-8e7d-9536de67c0ee.png)


임베딩된 두 단어벡터의 내적이 corpus 전체에서 단어가 동시등장하는 확률의 로그값이 되도록 정의

이에 맞추어 목적함수 J도 정의

특정 단어가 지나치게 빈도가 높아 Xij가 튀는 현상을 막기 위해 

$f(X_{ij}) = ({x \over x_{max}})^\alpha$ 로 정의해주어 f가 0~1의 값으로 나오도록 조정

### GloVe model Visualization

유사한 관계에 있는 단어들끼리 비슷한 Linear 속성을 갖게된다

![cs224n5](https://user-images.githubusercontent.com/99728502/226261719-f896e34d-e41d-4881-9718-b79407fd147c.png)


## Word embedding을 평가하는 방법

1. 내적(intrinsic) 평가
    - 평가를 위한 데이터에 적용하여 성능을 평가
    - 단어간의 유사성을 측정, 빠른 계산속도
    - 실제 현실에서 효과적인지 알기 힘듬
        
        Ex) correlation 평가(10점 만점)
        
        ![cs224n6](https://user-images.githubusercontent.com/99728502/226261734-11bc409b-6faa-49be-9845-a670c9e47cd2.png)

        

1. 외적(extrinsic) 평가
    - 실제 현실 문제에 직접 적용하여 평가
    - 각종 자연어처리 시스템에 직접 임베딩을 하여 성능 측정
    - 계산속도가 느림
    - 시스템의 문제인지, 아니면 두 시스템의 교호작용이 문제인지 알기 힘듬

## Word Ambiguity(모호성)

Ex) Pike

- 뾰족한 점 또는 지팡이
- 길쭉한 물고기의 일종
- 철도 노선 혹은 시스템
- 길의 종류
- 미래(coming down the pike)
- 자세의 일종
- 나아가다(pike along)

등등 많은 단어들은 여러개의 의미를 보유

- 동일한 단어의 여러 의미를 표현하는 방법
    1.  하나의 단어가 벡터 공간에서 서로 다른 클러스터를 형성하는 경우 
     해당 단어를 여러개로 분류해서 생성
        
        Ex) $idea_1,\ idea_2 \ idea_3$ 등등
        

1. Weighted Average(가중평균)
    
    → 한 단어의 서로다른 의미를 나타내는 벡터들에 가중치를 부여하여 가중평균 활용
    
    ex) $v_{pike} = \alpha_1v_{pike_1} + \alpha_2v_{pike_2}+...+\alpha_nv_{pike_n}$
    
    $\alpha_1 = {f_1\over f_1 + f_2 + ... + f_n}$, for frequency(f)
