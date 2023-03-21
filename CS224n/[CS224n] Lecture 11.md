# [CS224n] Lecture 11

# Question Answering

![1.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/1.png)

- 사람의 질문에 맞게 자연어로 자동적으로 응답하는 시스템
- 적절한 문서를 출력하는 정보 검색(Information Retriever)과 다르게, 간단한 구로 이루어진 정답을 출력
- 분류가 핵심
    - 어떠한 소스로부터 정보를 활용하는가?
        - 책의 구절, 웹 문서, 사진, knowledge bases, tables 등등등
    - 질문의 유형
        - 간단한 vs 복잡한, closed-domain(특정영역 한정) vs open-domain 등등
    - 응답의 유형
        - 짧은 텍스트 문구, 단락, 리스트, O X 등등

- 가장 흔한 예시

![2.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/2.png)

- QA의 구조
    - 대부분의 QA는 end-to-end training과 BERT같은 pretraining 기법을 기반으로 이루어짐

![3.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/3.png)

- 요즘에는 문자적인 QA를 넘어서, 이미지나 영상 등에서의 질문까지 답하는 시스템으로 발전
    
    ![4.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/4.png)
    

# Reading comprehension

![5.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/5.png)

- 정해진 구절을 이해한 뒤 그 내용에 관련된 질문에 답변하는 것
- ‘질문에 대해 답변할 수 있는 능력’이 가장 강력한 이해의 표시이므로, reading comprehension은 매우 중요
- 긴 문장에서 정보의 추출, 그리고 의미적인 역할의 라벨링이 가능

![6.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/6.png)

# Standford Question Answering dataset(SQuAD)

- 약 10만개의 주석이 달린 (구절, 질문, 정답) 3개 세트
    - 구절은 영어 위키페디아에서도 수집
    - 질문은 일반인들에게서 수집
    - 각 정답은 구절 속에 있는 짧은 문구로 작성
    - 각 정답은 3개의 Gold answer로 수집(다양한 정답이 있을 수 있기 때문)
- 평가방법 : Exact match(맞으면 1, 틀리면 0)과 F1-score(부분점수)를 함께 활용

# Neural models for reading comprehension

- Input : $C = (c_1,c_2, ...\ ,c_N), Q = (q_1, q_2, ... \ , q_M)$
- Output : $1 \le start \le end \le N$

- seq2seq model with attention

![7.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/7.png)

passage에 있는 어떤 단어가 질문과 가장 관련이 있는지를 알아야함

## BiDAF : Bidirectional Attetion Flow model

![8.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/8.png)

### BiDAF: Encoding

![9.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/9.png)

- 각각의 문맥과 쿼리 속의 단어에 대해 word embedding(GloVe)과 character embedding을 반복한 자료를 사용
    
    $e(c_i) = f([GloVe(c_i); charEmb(c_i]) \ \ \ \ \ e(qi) = f([GloVe(q_i);charEmb(q_i)])$
    
- 그런 다음, 문맥과 쿼리를 위한 contextual embedding을 만들기 위해 두개의 양방향 LSTM을 각각 사용

### BiDAF: Attention

![10.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/10.png)

- 먼저, 모든 짝 $(c_i, q_j)$에 대해 유사도 점수를 계산:
    
    $S_{i,j} = w^T_{sim}[c_i;q_j;c_i \odot q_j] \in R \ \ \ \ \ \ \ w_{sim} \in R^{6H}$
    
- context-to-query attention : 각각의 context 단어에 대해, 가장 관련된 단어를 query 단어중에서 뽑음
    
    $\alpha_{i,j} = softmax_j(S_{i,j}) \in R \ \ \ \ \ \ a_i = \sum_{j=1}^M \alpha_{i,j}q_j \in R^{2H}$
    

![11.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/11.png)

- query-to-context attention : 각각의 query 단어에 대해 가장 관련있는 context 단어를 하나씩 뽑음
    
    $\beta_i = softmax_i(max_{j=1}^M(S_{i,j})) \in R^N \ \ \ \ \ \ \ b_i = \sum_{i=1}^N \beta_ic_i \in R^{2H}$
    

![12.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/12.png)

- 최종 결과는 $g_i =[c_j;a_i;c_i \odot a_i;c_i \odot b_i] \in R^{8H}$
- Modeling Layer : context word 사이의 상호작용을 모델링
    
    $m_i = BiLSTM(g_i) \in R^{2H}$
    
- Output layer : start와 end position을 예측하는 두개의 분류기
    
    $p_{start} = softmax(w^T_{start}[g_i;m_i]) \ \ \ \ \ \ \ \ \ p_{end} = softmax(w^T_{end}[g_i;m_i']) \\m'_i = BiLSTM(m_i)$
    
- 최종 손실함수는 $L = -log(p_{start}(s^*)) - log(p_{end}(e^*))$로 표현

## Bert for reading comprehension

![13.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/13.png)

- BERT는 많은 양의 텍스트에 대해 pre-trained 된 deep한 양방향 Transformer encoder

![14.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/14.png)

- Question = Segment A
- Passage(구절) = Segment B
- Answer = Segment B에서 두개의 endpoint를 예측

- BERT와 BiDAF의 비교
    - BERT 모델은 110 million or 330 million 정도의 아주 많은 모수가 존재하는 반면, BiDAF는 ~2.5million parameter
    - BiDAF는 여러개의 양방향 LSTM을 통해 만들어지고, BERT는 Transformer을 통해 만들어짐
    - BERT는 pre-train 되었지만, BiDAF는 GloVe를 통해서만 만들어짐
    - BiDAF와 다른 모델은 질문과 구절의 상호작용을 모델링 하는데에 중점
    - BERT는 질문과 구절 사이에서 self-attention을 사용 → 둘이 근본적으론 유사

# Can we design better-pretraining objectives?

![15.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/15.png)

Two ides

- 15%의 무작위 단어 대신 근접한 단어들을 마스킹
- 마스킹된 단어의 예측을 할 때 2개의 엔드포인트를 사용
    
    $y_i = f(x_{s - 1}, x_{e+1}, p_{i-s+1})$
    

SQuAD 데이터셋에서 인간의 점수보다 이미 더 높은 점수를 기록했는데, 그렇다면 reading comprehension은 완벽한가?

- No, 상반되는 문구에 대한 reading comprehension의 성능은 낮음
- 그리고 하나의 데이터셋에서 훈련한 모델은 다른 데이터셋에 대해 일반화하지 못함

# Open-domain Question Answering

![16.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/16.png)

- 주어진 질문에 관련된 구절이 따로 없고, 위키페디아 같이 많은 문서를 모아둔 곳에 있는 구절을 활용
- 답이 어디있는지는 모르고, 하나의 open-domain 질문에 대해 답을 주는 것이 목표
    
    → 더욱 더 어렵지만 보다 더 현실적인 문제!
    

## Retriver-reader framework(검색-읽기 프레임워크)

- Retriver : $f(𝒟, Q)$  → $P_1, ... \ , P_K$,              𝒟: large collection of documents
- Reader : $g(Q, \left\{P_1, ...\ , P_K \right\})$ → A              A : an answer string

![17.PNG](%5BCS224n%5D%20Lecture%2011%204fd32615a4074b79bda07445fb68b21a/17.png)

- Retriever(검색기)도 훈련할 수 있음!
    - 각각의 구절은 BERT를 사용해서 vector로 인코딩 될 수 있고, retriver score은 질문에 대한 인코딩값과(벡터) 구절에 대한 인코딩 값의 내적을 통해 계산할 수 있음
    - 하지만, 너무나도 많은 구절이 있기 때문에 모든 벡터를 구하는데에 있어 어려움이 있음

- Reader 모델이 없어도 되는 가능성이 존재
    - 모든 구문을 인코딩하는게 가능, dense vector을 이용하여 BERT를 쓰지 않고, nearest neighbor search를 할 수 있음