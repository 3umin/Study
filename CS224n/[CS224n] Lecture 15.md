# [CS224n] Lecture 15

# Recap : Language models

<img width="199" alt="1" src="https://user-images.githubusercontent.com/99728502/226591173-3896236c-9fcc-47a2-9a81-caeb10000e08.png">

- Standard Language model : 시퀀스의 확률을 계산해 시퀀스의 다음에 올 단어를 예측

<img width="161" alt="2" src="https://user-images.githubusercontent.com/99728502/226591209-787d6d0c-e94e-4049-9b96-e9223e0b582a.png">

- Masked Language model : mask 된 토큰을 양방향적인 문맥을 활용해 예측

## Language model의 문제점

<img width="485" alt="3" src="https://user-images.githubusercontent.com/99728502/226591223-8eab0c37-c0a1-4c43-acee-00c2acd70c8f.png">

- 예측은 일반적으로 볼 때 맞는 것처럼 보일 순 있지만, 사실 정확하게 맞지는 않을 수 있음
- 이러한 현상의 이유
    - Unseen facts : 훈련 셋에 정답이 없었음
    - Rare facts : LM이 그 정답을 익히기 위해 충분한 샘플을 훈련받지 않음
    - Model sensitivity: LM이 훈련을 충분히 했지만, 프롬프트의 생성에 민감함
        - ex) ‘x was made in y’, ‘x was created in y’ → made와 created가 유사한 뜻이지만, 이 경우 다른 의미를 가짐
- 안정적으로 지식을 얻을 수 있는 능력의 부재
- 그래서 LM이 궁극적으로 전통적인 지식 기반을 대체할 수 있을까?

### Traditional knowledge base

<img width="497" alt="4" src="https://user-images.githubusercontent.com/99728502/226591238-406a7001-3386-4de4-be59-ecb43b3c6676.png">

- 이러한 방식으로 데이터가 구성되어 있으며, 루즈벨트가 태어난 날을 알고 싶다면,
    - SQL 문으로 ‘SELECT date of birth WHERE person = ‘Franklin D. Roosevelt’ ‘를 통해 가능

### Querying languge models as knowledge bases

<img width="483" alt="5" src="https://user-images.githubusercontent.com/99728502/226591248-fcce90d8-dd74-4985-90ea-f72bdc7e709b.png">

- 이러한 language model을 knolwedge base로 사용하는 데 있는 이점
    - LM은 많은 양의 unstructured, unlabeled text로 학습
    - LM은 좀 더 flexible함
- 하지만 hard to (interpret, trust, modify)

# Techniques to add knowledge to LMs

## Add pretrained entity embeddings

- 세상의 정보들은 entity로 이루어져 있음
- pretrained entity embedding은 다양한 entity에 대한 하나의 개념만을 가지고 있지 않음
- Word embedding과 비슷하게 entity embedding도 만들 수 있음(entity linking)
    - ‘U.S.A.’, ‘United States of America’, ‘Amrica’에 대해 하나의 entity embedding 적용
- Entity linking
    
    <img width="464" alt="6" src="https://user-images.githubusercontent.com/99728502/226591264-8be1392c-3023-4797-a0df-ae47bb77f53e.png">

    - 워싱턴에 대해, 조지 워싱턴(인물)로 이해할 수도 있고, 워싱턴(도시)로 이해할 수도 있는데, 문맥을 살피며 조지 워싱턴으로 선택
    
    <img width="208" alt="7" src="https://user-images.githubusercontent.com/99728502/226591287-cc97c126-ddaf-440e-bf22-5de493c33550.png">

    - 조지 워싱턴을 다른 건국영웅과 비슷한 벡터로 만드는 등의 원칙으로 entity embedding 진행
    - entity embedding의 훈련 방법
        - Knowledge graph embedding methods(TransE)
        - Word-entity co-occurrence methods(Wikipedia2Vec)
        - Transformer encodings of entity descriptions(BLINK)
- 그렇다면 어떻게 다른 embedding space에 있는 entity embedding을 만들까? → fusion layer
    - $h_j = F(W_t w_j + W_e e_k + b)$
    - $w_j$ : j번째 있는 단어의 embedding
    - $e_k$ : 그에 상응하는 entity embedding
    

### ERNIE(Enhanced language Representation with Informative Entities)

<img width="547" alt="8" src="https://user-images.githubusercontent.com/99728502/226591305-80199837-652e-45a7-aa3d-4ad9380976b7.png">

- Text encoder : multi-layer 양방향 Transformer encoder
- Knowledge encoder : 2개의 multi-headed attention과 fusion layer로 구성
- $h_j = \sigma(\tilde{W_t}^{(i)}\tilde{w_j}^{(i)} + \tilde{W_e}^{(i)}\tilde{e_k}^{(i)} + \tilde{b}^{(i)}) \\ w_j^{(i)} = \sigma (W_t^{(i)}h_j + b_t^{(i)}) \\ e_k^{(i)} = \sigma (W_e^{(i)}h_j + b_e^{(i)})$
- Maksed language model과 next sentence prediction, knowledge pretraining task로 학습
- $p(e_j|w_i)  = {exp(Ww_i \ · \ e_j)\over \sum_{k=1}^m exp(Ww_i \ · \ e_k)} \\ L_{ERNIE} = L_{MLM} + L_{NSP} + L_{dEA}$
- 장점
    - entity와 context를 fusion layer과 knowledge pretraining task로 결합
    - knowledge-driven task에 대해 성능 향상
- 한계
    - entity가 annotate 된 텍스트 데이터가 필요

### KnowBERT

- integrated entity linker을 BERT의 연장선으로 pretrain
- $L_{KnowBERT} = L_{NSP} + L_{MLM} + L_{EL}$
- downstream task에서 EL은 entity를 예측하기 때문에, entity annotation이 필요 x

## Use an external memory

- 기존의 entity embedding 방식은 가능한 entity embedding을 그냥 넣으면 되어서 편리하지만, knowledge base가 수정된다면 entity embedding과 model을 전부 다시 훈련해야함
- 모델이 외부의 정보를 활용할 수 있게 함으로써 더 직관적인 방법 확보
- 장점
    - 사실인 정보를 추가하고 업데이트하는데 더 편리
    - interpretable → 버그 등 확인 편리

### KGLM(Knowledge-Graphs for Fact-Aware Language Modeling)

<img width="531" alt="10" src="https://user-images.githubusercontent.com/99728502/226591346-8aec9ccb-c00a-45e6-9d56-a98793a35e54.png">

- LM에 Knowledge-graph를 조건화
- $P(x^{(t+1)}, \epsilon ^{(t+1)}|x^{(t)}, ... \ , x^{(1)}, \epsilon^{(t)}, ... \ ,\epsilon^{(1)}), \ \epsilon$ : set of KG entity
- LSTM을 사용해서 다음 단어의 entity를 예측(Related entity, New entity, Not an entity)

<img width="305" alt="11" src="https://user-images.githubusercontent.com/99728502/226591361-bd4e7f28-71ed-4c36-87c6-e68d2ad7e076.png">

- Related entity : LM hidden state와 relation embedding을 이용해 local KG에서 가장 점수가 높은 parent or relation을 선택
    - $P(p_t) = softmax(v_p  · h_t)$
- New entity : LM hidden state와 pretrained entity embedding을 이용해 full KG에 있는 가장 높은 점수의 entity를 선택
- Not an entity
    
    <img width="512" alt="12" src="https://user-images.githubusercontent.com/99728502/226591381-23567027-9165-4d69-a52e-36c6df0f4585.png">


### kNN-LM

<img width="925" alt="13" src="https://user-images.githubusercontent.com/99728502/226591423-e4a31187-2a33-4790-9792-d6eb69a461d4.png">

- 다음 단어를 예측하는 것보다, text sequence 사이의 유사도를 학습하는 것이 더 쉽다는 생각에서 출발
    1. datastore에서 k개의 가장 유사한 sequence of text를 찾음
    2. k sequence에 대해 corresponding value를 검색
    3. 최종 예측에 kNN probabilities와 LM probabilities를 결합
    - $P(y|x) = \lambda P_{kNN}(y|x) + (1-\lambda)P_{LM}(y|x)$

## Modify the training data

- 이전의 방법은 pretrained embedding이나 external memory로 확실하게 knowledge를 생성
- 데이터를 mask하거나 corrupt해서 지식을 implicitly하게 표현 가능
- 장점
    - 추가적인 기억 or 계산이 필요 x
    - 아키텍쳐에 대한 수정이 필요 x

### WKLM(Weakly supervised Knowledge-pretrained LM)

<img width="457" alt="15" src="https://user-images.githubusercontent.com/99728502/226591463-5939eb9e-3cdb-4ad9-b8e9-f362b3aca0d4.png">

- True, False를 구분할 수 있도록 학습

<img width="465" alt="14" src="https://user-images.githubusercontent.com/99728502/226591483-e99e29c7-c035-49d0-b5eb-c26ee6e9eca9.png">

- Negative knowledge statements를 만들기 위해 text 내의 mention을 같은 타입을 가진 다른 entity로 교체
    - 모델이 entity가 교체되었는지 아닌지를 예측
    - 형식 제약은 언어적으로 올바른 문장을 만들기 위해 존재
- MLM(Masked LM)은 token-level에서 정의되고, entRep은 entity-level에서 정의

# Evaluating knowledge in LMs

## LAMA(LAnguage Model Analysis)

- 얼마나 LM이 합리적인 지식을 가지고 있는가를 측정하는 분석
- KG triples와 question-answer pairs에서 cloze statement를 생성
- LM을 supervised relation extraction과 question answering system과 비교해서, pretrained LM에 내제되어 있는 지식을 평가
- 한계
    - 모델이 왜 좋은 성능을 내는지 이해하기 힘듦
    - 모델은 phrasing에 민감함

## LAMA-UHN(LAMA-UnHelpful Names)

- LAMA에서 relational knowledge 없이도 대답할 수 있는 샘플을 제거
- 그 결과 BERT가 예측을 할 땐 entity의 표면에 의존하는 경향이 있다는 것을 알게됨
- BERT의 LAMA 예측 점수가 8%까지 떨어지기도 함

## Developing better prompts to query knowledge in LMs

- LM은 사실을 알고있지만, 쿼리 그 자체 때문에 LAMA 같은 completion task에 실패함
- 즉 쿼리에 의해 모델 성능이 크게 좌우됨
- 더 많은 LAMA prompt를 생성하고 prompt ensemble을 통해 context의 다양성을 늘리면 성능이 더 좋아짐
