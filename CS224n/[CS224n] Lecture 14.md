# [CS224n] Lecture 14

<img width="537" alt="1" src="https://user-images.githubusercontent.com/99728502/226590842-620af265-a915-44f8-b3b0-a7f4b5ad6c58.png">

- pretraining - 비지도학습
- fine tuning - 지도학습

# Text-to-Text Transfer Transformer(T5)

<img width="538" alt="2" src="https://user-images.githubusercontent.com/99728502/226590859-2a389523-1d13-4dcb-b6b5-64ebe9d121ca.png">

<img width="454" alt="3" src="https://user-images.githubusercontent.com/99728502/226590871-ba4299d3-87b5-4531-8316-cfc96986a7da.png">


- Text as input, Text as output
- 번역, 텍스트 분류, 문장간유사도 측정, 요약 등 모든 NLP task를 통합

## Unlabeld text 훈련 방법

<img width="424" alt="4" src="https://user-images.githubusercontent.com/99728502/226590896-485c67bc-8661-42db-8523-0e8dab280f62.png">

- 몇 개의 토큰을 랜덤으로 고름
- 빈칸을 뚫어놓은 input을 주며, 빈칸을 채우는 것을 목적으로 훈련

# T5의 구조

<img width="446" alt="5" src="https://user-images.githubusercontent.com/99728502/226590915-55ee9773-cc1c-4818-aac7-5e16d27d1ad5.png">

- Pretrain
    - BERT와 동일한 크기의 인코더 디코더 transformer
    - T5 모델에서 새로이 제안한 C4 dataset 활용해서 훈련

- BERT은 텍스트의 분류, span prediction 등을 위주로 활용되지만, T5는 모든 NLP 기법에 활용가능
- Encoder - Decoder, Language model, Prefix Language model 중에선 Encoder-Decoder 모델이 가장 성능이 좋았음
- 결론적으로 Encoder-decoder 구조에, span prediction objective에 대해, C4 dataset으로 Multi-task pre-training으로 훈련을 한 뒤 더 큰 모델과 긴 시간동안 훈련을 하며 T5 모델 구축

## Multilingual model

<img width="529" alt="6" src="https://user-images.githubusercontent.com/99728502/226590937-94f86217-334f-4349-b2ca-05c28138a51f.png">

- T5에서 여러 언어를 통합

## How much knowledge does a language model pick up during pre-training?

<img width="436" alt="7" src="https://user-images.githubusercontent.com/99728502/226590951-6d6294da-c353-447f-9dee-5f6b6957660a.png">

- Reading Comprehension : 주어진 문맥에서 나온 질문에 응답

<img width="448" alt="8" src="https://user-images.githubusercontent.com/99728502/226590970-f55a7ad1-9e11-42d3-89c7-ea31f2756f2c.png">

- Open-domain Question Answering : 수많은 데이터베이스 속에서 찾아서 질문 응답

<img width="339" alt="9" src="https://user-images.githubusercontent.com/99728502/226590988-4a4392f1-6f2d-4196-8bfa-b841d4b352c1.png">

- Closed-Book Question Answering : pre-training때에 학습한 데이터를 바탕으로 응답
    - T5의 경우 사전학습을 대량의 데이터로 진행하기 때문에, Closed-Book 가능

## Do large language models memorize their training data?

<img width="284" alt="10" src="https://user-images.githubusercontent.com/99728502/226591003-aff9dbeb-e877-4fdd-9865-9341921d1491.png">

- Language model이 학습을 잘해서 정보를 이해하고 있는 것인지, 아니면 단순히 외우기만 하는것인지 여부
- 만약 단순 암기라면 GPT-2 같이 대량의 web 기반 데이터로 학습할 경우, 여러가지 문제 발생
    - 개인정보가 training dataset에 포함되어있고, 제대로 block되지 않을 경우 Large LM Decoder가 generation 하는 정보가 개인정보가 될 수 있음 ⇒ 윤리적 문제!

- 이러한 문제점을 해결하기 위해 학습 및 평가하는 방법

<img width="525" alt="11" src="https://user-images.githubusercontent.com/99728502/226591022-b8150f45-6fde-487e-9757-b7d0818ae03c.png">

## Can we close the gap between large and small models by improving the Trnasformer architecture?

<img width="450" alt="12" src="https://user-images.githubusercontent.com/99728502/226591032-fa047d75-1582-4ea9-9c18-67c66e29ebff.png">

기존의 T5 구조

- Factorized embedding matrix
- share the embedding matrix and the softmax output layer
- Mixture of softmaxes, adaptive softmax
- Different ways of normalizing, initializing the model
- different attention mechanism
- alternatives to attention mechanism 등등의 방법을 통해 개선시도
