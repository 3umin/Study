# [CS224n] Lecture 13

# What is Coreference Resolution?

- 같은 entity를 지칭하는 모든 mention을 식별하는 것
    
    ![1.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/1.png)
    
- 위의 문장의 경우, 같은 색의 단어는 모두 같은 의미
- 여기서 They와 같이 2개 이상의 entity를 포함하는 경우, split antecedents라고 부름
- NLP에선 이러한 split antecedents를 처리할 수 있는 기술이 없음
- 여기서 tree의 경우 Akash를 지칭하는데, 시스템에 따라 tree와 Akash를 같은 entity로 분류할 수도, 아닐 수도 있음
- Information extraction, question answering, summarization, machine translation, language modeling 등에 활용
- Machine Translation
    
    ![2.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/2.png)
    
    - 구글 번역기에선 학습된 문장을 통해 he 또는 she를 결정하게 되는데, 그 결과 각 성별에 대한 고정관념(여자이름, 남자이름 등)을 반영하기도 한다

# Coreference Resolution in Two Steps

![3.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/3.png)

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
    
    ![4.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/4.png)
    
    - 하지만 최근에는 잘 고려하지 않음, coreference 시스템에서도 mention을 발견하면 그 앞에 나오는 문장을 살피지 그 뒤에 오는 문장은 살피지 않는다.
- coreference model의 4가지 종류
    - Rule-based(pronomial anaphora resolution)
    - Mention Pair
    - Mention Ranking
    - Clustering

### Traditional pronomial anaphora resolution: Hobb’s naive algorithm

![5.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/5.png)

- him에 대한 reference를 찾고 싶음

![6.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/6.png)

### Knowledge-based Pronominal coreference

She poured water from **the pitcher** into **the cup** until **it** was full.

She poured water from **the pitcher** into **the cup** until **it** was empty.

- 여기서 두 문장은 같은 구조를 가지지만 it이 가리키는 것은 다름
- 이런 경우엔 Hobb’s algorithm을 사용할 수 없음
- 이러한 문제를 ‘Winograd Schema’라고 부름
- 사람이 사용하는 Common sense knowledge를 적용하면 이를 쉽게 해결할 수 있지만, 현재까지 이를 해결하는 방법은 나오지 않음.

### Mention pair

“I voted for Nader because he was most aligned with my values,” she said.

![7.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/7.png)

- 모든 pair of mention이 coreference를 가지는지를 Yes or No로 이진분류
- 이 경우 she와 (I, Nader, he, my)가 각각 coreferene를 가지는지를 확인하고, 그 결과 I와 my가 coreference를 가지는 것으로 나타남
- Training Mention Pair
    
    ![8.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/8.png)
    
- Testing Mention Pair
    
    ![9.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/9.png)
    
    - Transitive closure 사용 : A와 B가 coreference하고 B와 C가 coreference → A와 C도 coreference
- Disadvantage of Mention Pair Models
    
    ![10.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/10.png)
    
    - 만약 mention이 많이 들어있는 긴 문서가 있으면, 각각의 mention을 모두 찾아내서 yes라는 값을 배출하는 것이 아니라 한 mention을 잘 표현하는 특정한 mention 하나를 찾아내고 싶음 → mention ranking

### Mention Ranking

![11.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/11.png)

- 각각의 멘션에 대해 가장 높은 점수를 기록하는 antecedent를 찾음
- Dummy NA mention은 이전의 mention 중에 coreference를 못찾을 때 선택
- 각각의 확률은 통계적 분류기, 간단한 신경망, LSTM과 attention 그리고 transformer을 사용한 더 발전된 모델 등을 활용해서 계산할 수 있음
- Convolutional Neural Nets
    - idea : “특정 길이를 만족하는 모든 가능한 단어의 시퀀스에 대한 벡터를 계산하면 어떨까?”
    - CNN 방법을 통해 각 글자에 대해서 계산
    
    ![12.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/12.png)
    
    ![13.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/13.png)
    
    ![14.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/14.png)
    

### End-to-end Neural Coref Model

![16.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/16.png)

1. word embedding matrix와 character-level CNN을 이용해 계산
2. 양방향 LSTM에 학습
3. span representation을 계산
    
    ![15.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/15.png)
    
4. Attention score과 Attention distribution을 통해 final representation 계산
5. 마지막으로 coreference를 판정하기 위한 score을 계산
    
    ![17.PNG](%5BCS224n%5D%20Lecture%2013%203f8a07bb15964092adf8370d540c6224/17.png)
    

- 현재는 BERT-based coref가 가장 좋은 효과를 발휘