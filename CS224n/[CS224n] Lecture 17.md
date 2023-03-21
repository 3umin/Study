# [CS224n] Lecture 17

### Motivation : what are our models are doing?

![1.PNG](%5BCS224n%5D%20Lecture%2017%205b8c950519814ae59a97d96322b2b77b/1.png)

- 모델의 예측 정확도는 Accuracy 점수로 알 수 있지만, 모델이 어떻게 작용하는지는 알기 어려움
- 어떠한 Bias가 모델에 들어왔는가?

### Motivation : how do we make tomorrow’s model?

![2](https://user-images.githubusercontent.com/99728502/226595335-f05811f4-96d4-4208-b89d-89e53ffe3a00.png)

- Tomorrow’s model : 미래의 모델
- 적절한 모델 분석을 통해 Robust하고 예측 정확도가 높은 모델 생성

## Model analysis at varying levels of abstraction

- 모델을 분석하는 정말 많은 방법이 있지만, 완벽하거나 확실하게 명확한 방법은 없음
- 시작할 때, 모델의 어떤 부분을 추출해서 분석할 것인가?

![3](https://user-images.githubusercontent.com/99728502/226595383-e5bf5a12-41ba-459f-bd95-2461c3be06cd.png)

- 모델을 단순히 보면 확률로 나눌 수 있고, layer와 layer의 관계를 설명할 수도 있을 것이며, 조금 더 구체적으로는 layer와 layer 사이의 파라미터에 집중할 수도 있음

# Model evaluation as model analysis in natural language inference

![4](https://user-images.githubusercontent.com/99728502/226595405-5650efde-acb3-44b0-b6b3-41fd090e5757.png)

- 여러 NLP 모델은 훈련받은 데이터에 대해선 높은 정확도를 보였으나, 그렇지 않은 데이터에 대해선 매우 낮은 정확도를 보임
    
    → 결국 모델이 이 task를 학습하는 것이 아니라, 단순히 data를 fitting한다고 예상됨
    
- HANS(Heuristic Analysis for NLI Systems)
    - 모델이 Heuristic(체험적)인 방법을 통해 좋은 성능을 내는 것인지 확인하기 위한 용도의 데이터셋
    
    The chef who made the pizzas is here ← ‘Acceptable’
    
    The chef who made the pizzas are here ← ‘Unacceptable’
    
- 좀 더 acceptable 한 문장에 대해 더 높은 확률을 할당하는 방식으로 학습

![5](https://user-images.githubusercontent.com/99728502/226595416-96b03c7f-b8fb-487d-93c5-ae7f6b3a9ed4.png)

- 이러한 template을 이용하여 감성분석 모델이 잘 작동하지 않는 테스트셋을 만들 수 있음
- 위와 같은 사례를 통해, 모델이 task를 학습하는 것이 아니라 단순히 데이터를 fitting한다는 것이 확인됨

# Influence studies and adversarial examples

![6](https://user-images.githubusercontent.com/99728502/226595430-98e19536-df15-4f7f-845e-a14ff8e5ca8e.png)

- 모델은 long-distance context를 정말 활용할까?
    - Shuffle, reverse의 경우 50개 이후 loss의 변화 X
    - 50개 이후 단어의 identity는 중요

## Prediction explanations : what in the input led to this output?

- Saliency maps : 모델의 예측값에 각각의 input 단어가 얼마나 영향을 미쳤는지를 점수화한 것

![7](https://user-images.githubusercontent.com/99728502/226595450-374090d4-2b5a-400d-9f37-abe50a74aa25.png)

- idea : Simple gradient method
    - input words x1, x2, ... , xn이고 모델의 스코어링 함수가 S일때,
        
        $salience(x_i) = ||\nabla_{x_i}S_c(x_1, x_2, ... \ , x_n)||$
        
    - 결국 Gradient가 크다는 것은 score에 영향을 많이 줌을 의미
    - 하지만 gradient가 0인 경우에는 중요도를 잘 반영하지 못한다는 단점
    
- Explanation by input reduction
    
    ![8](https://user-images.githubusercontent.com/99728502/226595469-caf6311a-ffa0-445b-ba4b-e1e48c860517.png)

    - 같은 prediction을 갖는 가장 작은 input은? (rubbish example)
        - runnish example : 사람은 대답하기 힘들지만, 모델의 신뢰성은 높은 예제
- Analyzing models by breaking them
    
    ![9](https://user-images.githubusercontent.com/99728502/226595489-152801b9-3224-4a74-a6d1-3cf269b1e4e9.png)

    - idea : Paragraph에 문장을 추가하여 모델을 부셔보자!
        - 주황색이 새로이 추가된 문장
    - 추가된 문장이 질문의 답을 바꾸진 않았지만, 모델의 예측은 바꿈
        
        → 모델이 우리의 생각대로 동작하지 않음을 시사
        

# Analyzing representations

### Analysis of “interpretable” architecture components

![10](https://user-images.githubusercontent.com/99728502/226595583-9ef7c48c-8341-489e-9637-343ffbfa43c0.png)

- Idea : Attention map을 이용하여 각 layer가 어떤 역할을 하는지 추측
- 단어간 연결이 되어있기 때문에, 시각적으로 어떤 연관이 있는지 확인하기 쉬움
