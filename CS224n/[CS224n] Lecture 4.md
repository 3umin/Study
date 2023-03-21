# [CS224n] Lecture 4

# Synatic Structure: Consistency and Dependency

## Constitency Parsing

![4-1](https://user-images.githubusercontent.com/99728502/226567227-bbc859c2-f17a-43c2-91ee-c73def5b8ae4.png)

- 문장의 구성요소를 파악하여 문법적인 구조를 분석
- 각 단어들끼리 결합하여 어떠한 구(phrase)를 구성하고, 그 구는 다른 구와 또 결합해 문장이 됨
- 영어와 같이 어순이 비교적 고정적인 언어에서 주로 사용

ex) the(전치사) cat(명사) cuddly(부사) by(전치사) door(명사)

the cuddly cat(명사구), by the door(전치사구)

the cuddly cat by the door(명사구)

## Dependency Parsing

![4-2](https://user-images.githubusercontent.com/99728502/226567247-9f1e9a22-a992-4363-a09b-0e61818a4a06.png)

- 단어간 의존관계를 파악하는 구조 분석
- 단어간 관계를 의존관계(수식관계)로 표현
- 한국어와 같이 자유 어순을 가지거나 문장성분이 생략 가능한 언어에서 선호, 최근에는 영어권에서도 관심 증가
- head/governer : 수식을 하는 단어
- dependent/modifier : 수식을 받는 단어

large is modifiying crate

(in the kichen) is modifying kitchen

(by the door) is modifying door

(앞에 있는) in is dependent of crate / (뒤에 있는) in is dependent of kitchen

by is dependent of door

(in the kitchen) is dependent of crate

(by the door) dependent of crate

보편적 특징

- Bilexical Affinities : 두 단어 사이의 실제 의미가 드러나는 관계
- Dependency Distance : dependency의 거리(주로 가까운 거리에서 dependency 생성)
- Intervening Material : 마침표, 세미클론 같은 구두점을 넘어서 dependent 관계 형성되지않음
- Valency of Heads : head의 좌우측에 몇 개의 dependents를 가질 것인가에 대한 특성

# 문장 구조를 분석해야하는 이유

- Phrase Attachment Ambiguity : 형용사구, 동사구, 전치사구 등이 어떤 단어를 수식하는지에
    
    따라 의미가 달라짐
    
![4-3](https://user-images.githubusercontent.com/99728502/226567287-f798767e-e6d2-45e9-a87a-4b5638789e7a.png)

1. 샌 조스가 칼로 남자를 죽였다
2. 샌 조스가 칼을 든 남자를 죽였다

인간 뇌는 가능한 해석과 문맥에 맞는 것을 선택하는것에 능통하지만, 컴퓨터는 그렇지 않음

- Coordination Scope Ambiguity : 특정 단어가 수식하는 대상의 범위가 달라짐에 따라 의미 다름

![4-4](https://user-images.githubusercontent.com/99728502/226567304-7e456525-83f5-42b8-a1ee-b9128aa3bb01.png)

1. 우주선 베테랑이자 오랜 나사의 임원인 Fred Gregory가 이사로 임명되었다(한명)
2. 우주선 베테랑 A씨와 오랜 나사의 임원인 Fred Gregory가 이사로 임명되었다(두명)

# Dependency Grammar and dependency structure

![4-6](https://user-images.githubusercontent.com/99728502/226567325-f49f11d6-6500-46a3-aec9-fb1e1e855c57.png)

head인 submitted에서 시작해서

nsubj = subject

nmod = modifier

등등 Dependency Structure(의존구조)를 분석한 그림

## 구조를 시각화 하는 방법

### 1. Sequence 형태

![4-7](https://user-images.githubusercontent.com/99728502/226567347-0373bd05-457e-4abd-8cbb-5728849552b8.png)

- head에서 dependent로 화살표가 그려짐
- 가상의 노드 ROOT를 추가하여 모든 성분의 최종 head를 ROOT로 설정
- ROOT는 모든 단어가 최소 1개 노드의 dependent가 되도록 함

### 2. Tree Bank

![4-8](https://user-images.githubusercontent.com/99728502/226567360-9a6d8e16-c4c4-490f-9471-9969d29fce16.png)

트리뱅크의 장점

- 재사용성
- 몇가지 직감(intuition) 뿐 아니라 넓은 범위 커버
- 빈도 및 분포 정보
- NLP 시스템을 평가하는 방법으로 활용

# Dependency Parsing의 방법

1. Dynamic programming
2. Graph algorithms - CS161
3. Constranint Satisfaction - CS221
4. **Transition-based parsing**

### Transition-based parsing

- 두 단어의 의존여부를 차례대로 결정해나가면서 점진적으로 dependency structure을 구성
- stack, buffer, arcs 로 구성

![4-9](https://user-images.githubusercontent.com/99728502/226567428-d8ec6eac-a3ae-4e24-853c-bc3c434fdd6b.png)

Input

|

Buffer → Stack → Set of arcs

1. Buffer에 들어있는 각 토큰이 문장의 순서에 따라 Stack으로 이동
2. Stack에서 Decision 3개를 내림
    - Shift : Buffer에서 다음 토큰을 Stack으로 이동
    - Left-arc: 좌측으로 dependency가 결정
    - Right-arc: 우측으로 dependency가 결정

![4-10](https://user-images.githubusercontent.com/99728502/226567447-28756386-60fb-4c91-ab89-139b471e7bf4.png)

1. I ate까지 왔을때 I가 ate의 dependent이므로 I는 stack에서 사라지고 ate만 남음
2. Shift를 선택하고 Fish를 가져옴
3. fish가 ate를 dependent하므로 fish는 사라짐
4. 마지막으로 root가 최종적으로 남은 ate를 modify하면서 끝
