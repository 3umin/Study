# [CS224n] Lecture 10

# Word structure and subword models

<img width="521" alt="1" src="https://user-images.githubusercontent.com/99728502/226581507-00d76d1d-8cf8-470a-a231-d63f5c0d3aff.png">

- 수많은 단어들을 고정된 단어로 가정
- 모든 새로운 단어(오타, 파생) 등은 UNK로 일단 매핑
- 하지만 단어의 유한한 가정은 많은 언어에서 비현실적
    
    (ex. 스와힐리어 단어는 100개가 넘는 파생 뜻을 가질 수 있기 때문에, 각각의 인코딩은 수많은 정보를 담아야 함)
    
    ⇒ Byte-pair encoding 알고리즘, Pretraining 등을 통해 극복
    

### Byte-Pair Encoding Algorithm

<img width="522" alt="2" src="https://user-images.githubusercontent.com/99728502/226581570-71912216-f5a1-4abe-94d9-1ecfd078d8ca.png">

- 단어를 subword 토큰으로 부분적으로 나누어 학습
- 훈련과 테스트 때, 각 단어는 이미 알려진 여러개의 subwords로 구성된 시퀀스로 분리
- 과정
    1. 단어가 글자만을 포함하도록하고, 단어가 끝남을 알리는 신호 삽입(##이 없으면 끝)
    2. 텍스트 뭉치에서, 가장 인접한 글자를 subword로 저장
    3. 새로운 subword로 글자의 인스턴스를 대체. 원하던 단어 크기가 될때까지 반복

- 처음 보는 단어를 많이 사용하는 글자뭉치로 분리한 뒤에 학습시키면 나중에 모델이 taaaasty도 tasty로 이해할 수 있음
- transformerify 같이 기존의 단어끼리 합쳐진 단어는 transformer + ify로 분해하여 더 쉽게 이해
- 하지만 단어가 너무 많은 subword로 분해될 가능성이 있어 비효율적임

# Pretraining

## Pretraining with word embeddings

<img width="301" alt="3" src="https://user-images.githubusercontent.com/99728502/226581594-d280be44-d1b9-424f-8272-8b16ffe6b0e6.png">

- 문맥 정보가 없는 pretrained word embedding에서 출발
- LSTM이나 Transformer 등에서 어떻게 문맥을 구성하는지를 훈련
- 훈련 셋은 모든 언어의 문맥적 측면을 고려할 필요가 있는 한계 존재

## Pretrained whole model

<img width="275" alt="4" src="https://user-images.githubusercontent.com/99728502/226581613-6d470353-e807-4981-80f9-1fc1c0296c26.png">

- 현대 대부분의 NLP 네트워크가 전체 모델에 대한 pretraining으로 초기화됨
- pretraining 방법은 input 문장의 일부를 숨긴 뒤, 모델이 그 부분을 구성할 수 있도록 훈련
    
    → 언어의 표현, 모수 초기화 등에 매우 효과적
    

<img width="287" alt="6" src="https://user-images.githubusercontent.com/99728502/226581630-a12527ca-c2d7-48b6-aa67-f21471300a72.png">

- the, my, her, his, some, a 등등을 미리 학습시켜서 문장의 recreate 가능

## Pretraining through language modeling

<img width="256" alt="7" src="https://user-images.githubusercontent.com/99728502/226581653-c7ebac7a-d326-4d62-8452-1595ed2e63e4.png">

- 많은 양의 텍스트에서 language modeling을 수행할 수 있도록 신경망을 학습
    - step 1 : pretrain on language modeling(많은 양의 텍스트에서; 즉 일반적인 데이터를 학습)
    - step 2 : finetune - 원하던 데이터인지 아닌지 등을 따지며 미세 조정

# Pretraining model의 방법

<img width="226" alt="8" src="https://user-images.githubusercontent.com/99728502/226581669-593dbe68-486f-4470-a764-47b6f33b3b41.png">

1. Decoders
- 위에서 배운 Language models를 바탕으로 한 pretraining
- 새로운 문장을 만들기엔 좋지만, 새로운 단어에 대한 조건부 확률 계산 불가

1. Encoders
- 문맥의 양방향적 활용 - 미래 단어에 대해 계산 가능
- 하지만 pretrain 하는 확실하게 좋은 방법의 부재

1. Encoder - Decoders
- 마찬가지로 pretrain 하는 best 방법 부재

## Pretraining Decoders

<img width="258" alt="9" src="https://user-images.githubusercontent.com/99728502/226581685-28b19925-5e95-409e-a8be-c4caa9589ed5.png">

<img width="237" alt="10" src="https://user-images.githubusercontent.com/99728502/226581701-ed3e4e2c-de8e-45f1-916f-8f7dc603fea4.png">

$h_1, ...\ , h_T = Decoder(w_1, ...\,w_T)$

$y \sim Ah_t + b$  : 왼쪽의 방식 → Finetune 가능

$w_t \sim Ah_{t-1} + b$ : 오른쪽 방법 → 대화의 흐름 파악, 요약 등

- Generative Pretrained Transformer(GPT)

<img width="466" alt="11" src="https://user-images.githubusercontent.com/99728502/226581721-ee74be97-7e55-4d01-bf67-4a3345a5bd2c.png">

- 12 layer의 Transformer Decoder로 구성
- 문장을 entailing/contradictory/neutral 등으로 구분
- GPT보다 더 많은 훈련을 한 GPT-2, GPT-3도 탄생

## Pretraining Encoders

<img width="239" alt="12" src="https://user-images.githubusercontent.com/99728502/226581769-a1a04332-fa1b-44b5-a9c4-314cc8adc3ce.png">

- Encoder는 language model pretraining(Decoder)과 다르게, 양방향의 문맥을 활용
    
    → Language modeling 불가능
    
- Idea : 인풋 단어의 일부를 [MASK] 토큰으로 가리고, 그 단어를 예측

<img width="288" alt="13" src="https://user-images.githubusercontent.com/99728502/226581793-e4d14989-605a-4699-8395-b65939e80790.png">


- 랜덤으로 15%의 subword token을 예측
    - 80% 정도의 input 단어를 [MASK] 토큰으로 교체
    - 10% 정도의 input 단어를 무작위 단어로 교체
    - 나머지 10%의 단어는 그대로 유지
    
- BERT(Bidirectional Encoder Representations from Transformers)

<img width="497" alt="14" src="https://user-images.githubusercontent.com/99728502/226581822-e542d6ee-a598-42b9-be3e-718e667e2d45.png">

- BERT는 한 단어 뭉치가 다른 뭉치와 유사하게, 또는 랜덤으로 추출되었는지를 예측하기 위해 훈련됨
- 매우 인기있고 다목적으로 활용 가능

### Pretrained encoder의 한계점

<img width="561" alt="15" src="https://user-images.githubusercontent.com/99728502/226581845-0c6ceaf4-a42e-4d07-8e0d-866dc9db25a4.png">

- 문장 생성을 해야하는 경우, Encoder보단 Decoder를 활용하는 것이 훨씬 유용함

## Pretraining Encoder-decoder

<img width="249" alt="16" src="https://user-images.githubusercontent.com/99728502/226581854-d26e2107-1d8f-4131-ae56-6f682c626bb8.png">

$h_1, ...\ , h_T = Encoder(w_1, ...\,w_T)$

$h_{T+1}, ...\ , h_{2T} = Decoder(w_1, ...\,w_T, h_1, ...\,h_T)$

$y_i \sim Aw_i + b, i >T$  

<img width="673" alt="17" src="https://user-images.githubusercontent.com/99728502/226581871-18ddbf32-3647-4606-936f-84505c1ebf0a.png">

- 인코더와 디코더 방식의 장점을 결합한 방법
- input 문장에 target을 없앤 다음 target을 decoder에서 출력 목표

## GPT-3

<img width="553" alt="18" src="https://user-images.githubusercontent.com/99728502/226581883-11de8fca-175f-4c5a-9699-62ec2e6e563e.png">

- 아주 큰 Language model(1750억개에 달하는 모수 존재)
- Gradient step을 활용하지 않고, 단순히 문맥 예시를 확인해가며 학습
