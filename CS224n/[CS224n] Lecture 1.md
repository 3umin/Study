# [CS224n] Lecture 1

# 언어의 의미를 표현하는 방법

### ‘의미’라는 단어의 뜻?

언어학적 관점에서는 **표시론적 의미론**(Denotational Semantics)

→ 의미를 사물이 나타내는 것

(ex. 의자 → 의자와 같은 모든것, 달리기 → 달리기 라는 단어를 생각했을 때 떠오르는 일련의 행동)

이러한 뜻에 대해 컴퓨터적으로 접근하는 것은 어려움

일반적으로 접근하는 방법은 **wordnet**을 통해 단어의 사전적 의미와 단어 사이의 관계(동의어, 유의어 등)을 알아내는 것

### Wordnet의 한계점

- 뉘앙스를 많이 놓치게 됨

       ex) proficient 는 동의어로 good이 나오지만, 일부 문맥에서만 적합함

- 단어들의 새로운 의미를 계속 업데이트하기 힘듬
- 주관적인 의미
- 사전을 생성하고 적용하는데 인간의 노동력이 필요
- 단어의 유사성을 정확하게 계산할 수 없음

## 전통적인 NLP

![캡처3](https://user-images.githubusercontent.com/99728502/226260322-29c5ca64-25a2-4004-a98b-bff838b19c43.png)

**One-hot-vector** : 각 단어별로 위치를 지정해 해당 단어의 위치에만 1을 채워 표현

즉 단어가 10개면 10개 원소를 갖는 서로다른 벡터 10개가 나오는 것

- 단어가 매우 많이때문에 단어가 많아질 수록 벡터가 너무 길어짐
- 단어간의 유사성을 측정하기 어려움
    
    ex) 호텔과 모텔은 유사한 단어이지만, 수학적으로 두 단어의 벡터는 직교 → 유사관계 측정 X
    

## 현대의 NLP

![cs244n](https://user-images.githubusercontent.com/99728502/226260357-17927e71-1322-489b-b918-c1df7610bde6.png)

**분산의미론**(Distributional Semantics): 단어의 의미를 단어 주변에 자주 나타나는 단어로 파악

- **Context words**(문맥 단어): 단어 w의 주변에 나타나는 단어 집합
    
    ex) ‘banking’의 의미를 정의할때, 단어 앞뒤로 붙어서 오는 context words가 banking을 표현함
    
- 단어가 나타나는 문맥을 보고 해당 단어의 뜻을 예측
- **Word embedding**: 단어의 유사도를 벡터화 하는 작업

보통 워드벡터의 차원은 50에서 300사이정도인데, 여기선 간단하게 9차원으로 나타냄

![캡처4](https://user-images.githubusercontent.com/99728502/226260516-ef0fe018-0e41-464b-a397-020e3513ee96.png)

![캡처5](https://user-images.githubusercontent.com/99728502/226260521-479621d2-c65e-4605-86d2-c435e2c97dc8.png)

우측 이미지는 100차원의 벡터를 2차원으로 압축해 표현한 것으로, 단어간 간격이 가까울수록 유사한 문맥에서 나타난다.

## Word2vec 알고리즘

![캡처6](https://user-images.githubusercontent.com/99728502/226260550-d91fcecb-39aa-4a79-8ab1-5b0728809517.png)

- 모든 단어들은 벡터로 표현됨
- 텍스트 내 각각의 위치 t를 지나면서 중심단어 c와 문맥단어 o를 갖는다.
- c와 o의 워드벡터간 유사성을 활용해 c가 주어졌을 때의 o의 확률을 계산
- 이 확률을 최대화하는 벡터를 조정
- 
![캡처7](https://user-images.githubusercontent.com/99728502/226260569-1d59e47c-360c-4f4a-b8bb-89e03c4822f3.png)

![캡처8](https://user-images.githubusercontent.com/99728502/226260575-f11723dd-742f-452a-8a7e-4d1b143d5574.png)

- L(θ), likelihood : 모든 단어(t)에 대해 fixed size window(m)의 단어만큼 o가 나올 확률의 곱
- **J(θ), 목적함수(손실함수)** : negative log likelihood 형태, 이를 최소화하는 θ의 값을 구함
    
                                             → 그 과정을 통해 예측의 Accuracy 최대화 가능
    

 

### $P(w_{t+j} |w_t;θ)$를 계산하는 방법

- $\nu_w:$ **w가 center word(중심단어)일 때**
- $**u_w:$ w가 context word(주변단어)일 때**

![캡처9](https://user-images.githubusercontent.com/99728502/226260590-a23ed1a7-de91-4676-b065-025f75f8ed51.png)

![캡처10](https://user-images.githubusercontent.com/99728502/226260599-4751444e-4462-4617-a460-ecbd42b12171.png)

1. 내적을 통해 o와 c의 유사도를 비교
2. 양수로 만들기 위해 exponentiation을 진행
3. 확률 분포를 위해 전체 단어에 대해 정규화 진행

### 최적화(Optimization)

J(θ**)를 최소화하기 위해 편미분을 통한 수식전개**

${\partial\over\partial v_c}log{exp(u_0^Tv_c)\over\sum_{w=1}^{v}(u_0^Tv_c)} = u_0 - \sum_{x=1}^{v}P(x|c)*u_x$

→ **실제 문맥 단어**(좌변) - **예측된 문맥 단어**(우변)

    그 차이가 작을 수록 모델의 성능은 좋아짐
