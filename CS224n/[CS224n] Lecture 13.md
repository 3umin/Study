# [CS224n] Lecture 13

# What is Coreference Resolution?

- 같은 entity를 지칭하는 모든 mention을 식별하는 것
    
    ![1](https://user-images.githubusercontent.com/99728502/226586443-bc17bc77-9979-4fbb-967b-eac7eccf5380.png)

- 위의 문장의 경우, 같은 색의 단어는 모두 같은 의미
- 여기서 They와 같이 2개 이상의 entity를 포함하는 경우, split antecedents라고 부름
- NLP에선 이러한 split antecedents를 처리할 수 있는 기술이 없음
- 여기서 tree의 경우 Akash를 지칭하는데, 시스템에 따라 tree와 Akash를 같은 entity로 분류할 수도, 아닐 수도 있음
- Information extraction, question answering, summarization, machine translation, language modeling 등에 활용
- Machine Translation
    
    <img width="364" alt="2" src="https://user-images.githubusercontent.com/99728502/226586464-5c3f98d7-2840-4512-b9c6-d8e0c32433ca.png">

    - 구글 번역기에선 학습된 문장을 통해 he 또는 she를 결정하게 되는데, 그 결과 각 성별에 대한 고정관념(여자이름, 남자이름 등)을 반영하기도 한다

# Coreference Resolution in Two Steps

<img width="358" alt="3" src="https://user-images.githubusercontent.com/99728502/226586480-8678655a-922d-4cb4-aecb-765befef38fc.png">

- mention을 탐지한 다음에, 그 멘션들을 집단화

## Mention Detection

- Mention을 3가지 종류로 나눌 수 있음
    1. Pronous : I, your, it, she, him, etc.
    2. Named entities : Parias, Joe Biden, Nike
    3. Noun phrases : ‘a dog’, ‘the big fluffy cat stuck in the tree’
- 각 종류별로 다른 NLP 시스템 사용해서 탐지
    1. Pronous - part of speech tagger
    2. Named entities - Named Entity Recognition system
    3. Noun phrases - parser(특히 constituency parser 사용!)
- 위에 말한 3가지 조건을 만족하지만, mention이 아닌 것도 있음
    - **It** is sunny
    - **Every student**
    - **No student**
    - **The best donut in the world**
    - **100 miles**
- 이러한 안좋은 mention들을 걸러내는 방법
    - Classifier 학습
    - Classifier 없이 coreference system을 돌려도 하나만 있는 mention을 삭제하기 때문에, 신경안쓰고 그냥 진행하는게 가장 일반적

- 3가지 종류의 mention별로 POS tagger, NER, parser 등을 따로 사용하지 않고도, 한번에 진행할 수도 있음
    
    → mention-detection과 coreference resoultion을 2단계로 나누지 않고 end-to-end로 한번에 진행
    

## Coreference

- coreference : mention이 같은 entity를 지칭하는 것
    
    ex) **Barack Obama** traveled to ... **Obama** ...
    
- anaphora : 문장 속에서 앞에 나온 단어를 지칭하는 것
    
    ex) **Barack Obama** said **he** would sign the bill.
    
- coreference와 anaphora는 유사하지만 엄밀히 다른 개념임!
- 명사구가 reference가 없는 경우도 있음
    
    ex) **No dancer** twisted **her knee**
    
    → her은 No dancer에 대한 anaphora인데, No dancer는 특정한 reference를 지칭하지 않으므로, 어떠한 entity를 가지지 않음
    
- anaphoric한 관계가 항상 coreferential하지 않음
    
    ex) We went to see **a concert** last night. **The tickets** were really expensive.
    
    → 여기서 ticket은 concert의 ticket이므로 anaphora라고 할 수 있지만, 서로 같은 entity를 가지진 않으므로 coreference는 없으며, 이런 경우를 bridging anaphora라고 칭함
    

- cataphora : anaphora가 앞에 나온 단어가 아니라, 뒤에 나올 단어를 지칭하는 것
    
    <img width="379" alt="4" src="https://user-images.githubusercontent.com/99728502/226586515-046810e8-4a0e-496a-b186-614c04b2a21b.png">

    - 하지만 최근에는 잘 고려하지 않음, coreference 시스템에서도 mention을 발견하면 그 앞에 나오는 문장을 살피지 그 뒤에 오는 문장은 살피지 않는다.
- coreference model의 4가지 종류
    - Rule-based(pronomial anaphora resolution)
    - Mention Pair
    - Mention Ranking
    - Clustering

### Traditional pronomial anaphora resolution: Hobb’s naive algorithm

<img width="496" alt="5" src="https://user-images.githubusercontent.com/99728502/226586540-603715f4-ff09-4ae3-b8c1-8a66341417b0.png">

- him에 대한 reference를 찾고 싶음

<img width="522" alt="6" src="https://user-images.githubusercontent.com/99728502/226586557-fd12cb13-5f45-4135-9210-64b73b157196.png">

### Knowledge-based Pronominal coreference

She poured water from **the pitcher** into **the cup** until **it** was full.

She poured water from **the pitcher** into **the cup** until **it** was empty.

- 여기서 두 문장은 같은 구조를 가지지만 it이 가리키는 것은 다름
- 이런 경우엔 Hobb’s algorithm을 사용할 수 없음
- 이러한 문제를 ‘Winograd Schema’라고 부름
- 사람이 사용하는 Common sense knowledge를 적용하면 이를 쉽게 해결할 수 있지만, 현재까지 이를 해결하는 방법은 나오지 않음.

### Mention pair

“I voted for Nader because he was most aligned with my values,” she said.

<img width="348" alt="7" src="https://user-images.githubusercontent.com/99728502/226586577-c8c4e2a4-6b0d-4e7b-9d33-e9ca9bbe2420.png">

- 모든 pair of mention이 coreference를 가지는지를 Yes or No로 이진분류
- 이 경우 she와 (I, Nader, he, my)가 각각 coreferene를 가지는지를 확인하고, 그 결과 I와 my가 coreference를 가지는 것으로 나타남
- Training Mention Pair
    
    <img width="390" alt="8" src="https://user-images.githubusercontent.com/99728502/226586598-d6af4f23-5e27-4e70-8610-f3a57a265a87.png">

- Testing Mention Pair
    
    <img width="357" alt="9" src="https://user-images.githubusercontent.com/99728502/226586612-dca53e6a-8160-4a53-866f-6026d0ab5ed3.png">

    - Transitive closure 사용 : A와 B가 coreference하고 B와 C가 coreference → A와 C도 coreference
- Disadvantage of Mention Pair Models
    
    <img width="441" alt="10" src="https://user-images.githubusercontent.com/99728502/226586634-d06a5df3-99a6-4af7-8d2c-75298484620c.png">

    - 만약 mention이 많이 들어있는 긴 문서가 있으면, 각각의 mention을 모두 찾아내서 yes라는 값을 배출하는 것이 아니라 한 mention을 잘 표현하는 특정한 mention 하나를 찾아내고 싶음 → mention ranking

### Mention Ranking

<img width="411" alt="11" src="https://user-images.githubusercontent.com/99728502/226586653-09b60dbf-3e1c-4c12-95ff-f398ebc6c0d1.png">

- 각각의 멘션에 대해 가장 높은 점수를 기록하는 antecedent를 찾음
- Dummy NA mention은 이전의 mention 중에 coreference를 못찾을 때 선택
- 각각의 확률은 통계적 분류기, 간단한 신경망, LSTM과 attention 그리고 transformer을 사용한 더 발전된 모델 등을 활용해서 계산할 수 있음
- Convolutional Neural Nets
    - idea : “특정 길이를 만족하는 모든 가능한 단어의 시퀀스에 대한 벡터를 계산하면 어떨까?”
    - CNN 방법을 통해 각 글자에 대해서 계산
    
    <img width="547" alt="12" src="https://user-images.githubusercontent.com/99728502/226586670-dbfec666-af3a-4649-854f-68dbdc9ea143.png">
    
    <img width="425" alt="13" src="https://user-images.githubusercontent.com/99728502/226586678-42242686-4c44-4b13-9ded-d13327947635.png">

    <img width="509" alt="14" src="https://user-images.githubusercontent.com/99728502/226586706-4e0945ae-94c0-489c-ade6-0958770ee8b8.png">


### End-to-end Neural Coref Model

<img width="401" alt="16" src="https://user-images.githubusercontent.com/99728502/226586732-0f2227e4-2c6f-4c80-b23a-ef5ac839e0cc.png">

1. word embedding matrix와 character-level CNN을 이용해 계산
2. 양방향 LSTM에 학습
3. span representation을 계산
    
    <img width="388" alt="15" src="https://user-images.githubusercontent.com/99728502/226586750-74fcaa23-9099-4375-b277-500e8240aee1.png">

4. Attention score과 Attention distribution을 통해 final representation 계산
5. 마지막으로 coreference를 판정하기 위한 score을 계산
    
    <img width="392" alt="17" src="https://user-images.githubusercontent.com/99728502/226586783-b4813470-8054-41eb-af3a-19d6ab4f56e3.png">


- 현재는 BERT-based coref가 가장 좋은 효과를 발휘
