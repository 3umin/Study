# [CS224n] Lecture 16

# Language & People

- 가장 흔한 오해 중 하나는 언어가 단어, 그리고 그들의 의미와 관련되어 있다고 생각하는 것 → 언어는 단어가 아니라 사람과 관련이 있음
- 우리가 사용하는 데이터, 기법, 도구들에 대해 내리는 결정은 사람과 사회에 미치는 영향을 고려해야함

# What is ethics?

- ‘Ethics is a study of what are good and bad ends to pursue in life and what it is right and wrong to do in the conduct of life.’
- 그렇기 때문에, 윤리는 매우 practical함
- 뭐가 good인지, right인지를 판단하는 방법?
    - Trolley dilemma(5명과 1명 중 누구를 죽일 것이냐)
    - Chicken dilemma(계란을 보고 암수를 판단하는 훈련기를 만들어서, 이것을 암탉으로 키울 것이냐 치킨으로 먹을것이냐 결정)
    - → 윤리는 그저 흑백논리에 의해 정해질 수 없으며, 쉽게 정답을 내놓지 못 할 수 있음
- ex) 사람들의 사진과 글을 바탕으로 IQ를 예측하는 classifier을 훈련할 때
    - 누가 이러한 분류기로 인해 이득을 볼 것인가?
    - 누가 이러한 분류기로 인해 상처를 받을 것인가?
    - 특정 집단에 대해 다른 정확도를 나타낸다면, 데이터가 편향적일 수 있음
    - 결국에 이러한 IQ Classifier과 egg classifier이 무엇이 다른지...

### AI Gaydar(게이 탐지력)

- 사람의 얼굴을 보고 성적 지향을 가지는지 식별하는 문제
- 딥러닝 모델은 얼굴적인 특징과 꾸밈의 정도 등을 활용해 예측에 활용
- 남자는 81%의 정확도, 여자는 74%
- 정확도는 좋았지만, 얼굴을 보고 이 사람이 게이인지 아닌지 판단하는게 윤리적으로 적합할까?
    - 많은 나라에서 게이인 사람은 고발당할 수 있음(법적으로든, 사회적으로든)
    - 고용, 가족관계, 헬스케어 기회 등 여러 분야에 영향을 끼칠 수 있음
    - 성별, 인종, 성적 지향, 종교 등은 사회적인 개념이기 때문에, 이분법적이지 않을 수 있고, 시간에 따라 변할 수 있으며, 개인적인 사안임
    - 가장 중요한 것은, 이러한 특성에 따라 사람들이 차별받을 수 있음
- 훈련에 사용된 데이터는 미국의 데이팅 사이트에서 다운로드 되었음
    - Legal이며, Public한 것은 맞지만 ethical과 publicize 측면에서 잘못됨
    - 데이터로 사용된 사람들이 이에 동의하였는가?
- 훈련에 사용된 데이터 또한 편향 존재
    - 백인만 포함되었으며, 본인이 직접 성적 지향을 밝힌 사람만 사용
    - 특정한 사회적 집단, 특정한 연령 집단, 특정한 시간대
    - 사진은 연구자들의 이목을 끄는 사진만 사용
- 잘못된 분류에 따른 사회적인 문제도 발생할 수 있음
    - 개와 쿠키를 잘못 분류한 것은 웃고 넘어갈 수 있지만, 사람과 개를 잘못 분류한다면?

![1](https://user-images.githubusercontent.com/99728502/226591691-2e19b88e-629b-418a-8309-b558b71c90c0.png)

![2](https://user-images.githubusercontent.com/99728502/226591719-3721996d-842f-4174-84d9-e1d6b8ec754a.png)

### AI system을 만들 때 고려해야 할 사항

- 기술의 영향과 잠재적인 사용처 : Who could benefit (or be harmed) by such a technology?
- Privacy : 누가 데이터를 소유하는가?, Published vs. Publicized
- 데이터의 편향
- 모델의 편향
- 정확도를 넘어서 utility-based 평가 : the cost of misclassification 등
- 이러한 사항들은 점점 중요해져감
    - 데이터가 유저가 직접 만든 내용으로 들어가는 경우가 많아지고 있고
    - 머신러닝 툴이 점점 널리 퍼지고 있음

### Topics in the intersection of Ethics & NLP

- 알고리즘 편향 : 데이터와 NLP 모델의 사회적 편향
- 무례(incivility) : 증오, 혐오 등
- 사생활 침해
- 가짜정보
- 기술적 차이 : 불공정한 NLP 기법, 소수자를 과소평가 등

## Algorithm bias : social bias in data & models

- **Giggle(낄낄)** - Laugh(웃음) 중에 어떤 표현이 여자에게 더 많이 사용될까?
- Brutal(잔혹한) - **Fierce(사나운)** 중에 어떤 표현이 여자에게 더 많이 사용?
- **Impressive** - Amazing 중 어떤 표현이 노인에게 더 많이 사용될까?

## Why do we intuitively recognize a default social group?

- Implicit bias(암묵적 편향)

![3](https://user-images.githubusercontent.com/99728502/226591748-d1b59570-3106-4513-bf29-648dc64ba588.png)

- 우리의 뇌는 두 부분으로 나뉨
    - System 1 : make decision without thinking
    - System 2 : Logical product
- System 1은 우리가 내리는 대부분의 결정에 관여
- 그렇기 때문에, 우리의 정신은 간단히 처리하기 위해 Categorize, Store information in mental representations, automatically activate stored information → 이러한 특성 때문에 Cognitive bias는 생길 수 밖에 없음

## Common biases that affect how we make decisions

- confirmation bias : 이전의 생각을 강화해주는 정보에 더 신경씀
- ingroup favoritism : 같은 그룹 안에 있는 사람을 더 좋아함
- group attribution error
- halo effect : 사람에 대한 전반적인 인상이 그들의 특정한 특성에 대한 평가에 영향
- just-world hypothesis : 희생자를 비난함으로써 현재가 공평하다고 주장
- 사회적 고정관념(성별, 인종, 장애, 연령, 성취향, 문화, 언어, 종교 등)

### How do implicit biases manifest?

- Microagression
    - 의도하지 않더라도 무의식적으로 나타나는 편견적 태도
    - Girls just aren’t good at math
- Bias in language
    - 고정관념, 선입견, 혐오 등
    - 역사적인 인간의 편견
- Bias in datasets
    - 데이터 선택/추출 편견
    - 연구 진행자의 선택/인식 편견
- From social bias to algorithmic bias
    - AI는 위에 있는 뇌의 시스템 중에 system 1만 모방
    - 데이터 중심적 모델(문화적, 사회적 맥락 없음)
- 현재의 NLP는 명시적인 toxic language에 집중했지만, 은연중에 드러나는 negativity는 잡아내지 못함
- 이러한 문제점은 이미지 검색에서도 나타남
    - 구글에 ‘three black/white teenager’을 검색했을 때
    
    ![4](https://user-images.githubusercontent.com/99728502/226591833-ada6a5ac-7cc0-4f87-a2e3-656acb47a1ad.png)

    ![5](https://user-images.githubusercontent.com/99728502/226591850-bcb5372e-6ef2-48f9-a971-d2791c2aa89d.png)

    - ‘Doctor’을 검색했을 때 남자 위주, ‘Nurse’를 검색했을 때 여자 위주로 나옴
- Bias in Machine Translation
    
    ![6](https://user-images.githubusercontent.com/99728502/226591875-b816339f-3120-471f-a96a-13fff843b334.png)

    - 원어는 성별에 대한 언급이 전혀 없는 상태임
    
    ![7](https://user-images.githubusercontent.com/99728502/226591895-d023129c-4bdc-43fe-9263-912e7269fb8b.png)

    - 현재는 개선된 상태
- Bias in dialogue systems
    - 사용자와의 대화로부터 추가적인 학습을 하는 AI 챗봇의 경우 차별적인 언행을 배운 사례가 많음(Tay, 이루다 등)
- 이러한 편견을 탐지하는 여러 연구가 현재 진행중
