# Lecture 2

# Image Classification : Core task in CV

- 이미지를 입력받아 미리 정해놓은 레이블 중에 하나로 분류
- 사람에게는 매우 쉽지만, 컴퓨터에게는 어렵다
(컴퓨터는 이미지를 각 픽셀별로 가지고 있는 수치로만 인식하기 때문에, 이미지에 아주 미묘한 변화만 주더라도 픽셀 값은 모조리 변하게 됨)
- 알고리즘은 화각, 조명, 변형, 가려짐, 배경 보호색, 다양성 등의 문제에도 강인하게 대응해야 함

## Image Classification 방법

- 분류를 위해 Edge를 계산한 뒤, 다양한 코너와 Edge를 하나의 카테고리로 분류해, ‘명시적인 규칙 집합’을 정의하는 방법
    - 이런 알고리즘은 강인하지 않으며, 또 다른 객체를 인식해야 한다면 규칙을 처음부터 새로 또 만들어야 하기 때문에 확장성이 없음
    - 이 세상에 존재하는 다양한 객체들에 유연하게 적용 가능한 방법이 필요! 
    → Data-Driven Approach
- Data-Driven Approach
    - 규칙을 써내려가는 대신에 라벨이 있는 이미지를 수집한 다음에, 이 데이터셋을 이용해 ML Classifier을 훈련하여 모델을 사용

- Nearest Neighbor(NN)
    - 훈련 과정에서 모든 학습 데이터를 기억
    - 모델에 이미지가 들어왔을 때 훈련 이미지 중 가장 가까운 이미지의 라벨로 예측
    - 가장 가까운지를 어떻게 비교하는지는 여러가지 Distance Metric이 존재
        - L1 distance =  $d_1(I_1, I_2) = \sum_p |I_1^p - I_2^p|$
        - L2 distance = $d_2(I_1, I_2) = \sqrt{\sum_p (I_1^p - I_2^p)^2}$
        - L1 Metric은 좌표의 축이 바뀐다면 값이 달라지지만, L2는 변하지 않음
        - 그래서 각각의 컬럼이 개별적인 의미를 가지고 있다면 L1이 더 잘 어울릴 수 있음(ex. 키-몸무게)
        - 종속변수 값을 예측하는 데에 어떤 변수가 중요한지 알면 L1, 모르면 L2 (L1은 중요하지 않은 변수값을 0으로 처리할 수 있기 때문)
        - 요소들간의 실질적인 의미를 잘 모르는 경우라면 L2가 더 나을 수 있다.
    - 하지만 이 방법은 Test time에 N개의 데이터와 인풋이미지를 모두 비교해야하기 때문에, 너무 느림
    
    
        <img width="535" alt="Untitled" src="https://user-images.githubusercontent.com/99728502/226164907-fadad8f9-0b75-40f9-ba88-54dc18312229.png">
        
    - 그래서 NN을 좀 더 일반화시킨 위와 같은 KNN 방법이 사용
        - 가까운 이웃을 K개 만큼 찾고, K개의 포인트에서 투표를 하여 가장 많은 득표를 얻은 클래스로 분류
        - k가 높아질 수록 잡음에 더 강해짐
    - KNN을 사용한다면 K 값, L1/L2 Distance 등 내 문제와 데이터에 적합한 하이퍼파라미터를 찾아야 함
        1.  ‘학습 데이터’에 최적 성능을 보여주는 조합을 찾는 방법(Bad)
        2. train/test 데이터로 나눈 후 test data에 가장 최적인 조합을 찾는 방법(Bad)
            - 머신러닝 모델은 한번도 보지 못했던 데이터에서 성능이 좋아야하기 때문에, 2번째 방법도 그저 ‘테스트 셋’에서만 성능이 좋은 모델을 정할 수 있음
        3. 가장 효과적인 방법은 데이터를 Train/Val/Test로 나누어 validation set으로 검증을 해 최적의 성능을 가진 모델을 정한 뒤 Test 셋으로 단 한 번만 예측을 진행해 성능을 기록
        4. 또 다른 방법은 Cross-Validation. validation 셋을 계속 바꿔가며 검증
        
        <img width="487" alt="Untitled 1" src="https://user-images.githubusercontent.com/99728502/226164920-84b7ddd0-eda6-4f3c-87a4-1b21da131ad6.png">
        
        - 이 방법이 거의 표준이긴 하지만 딥러닝 같은 큰 모델을 학습시킬 때는 연산량이 많기 때문에 잘 사용하지 않음
    - 하지만 KNN은 이미지에서 잘 사용되지 않음
        - Test time에 너무 작동이 느리며
        - Distance Metrics가 효과적이지 못하기 때문
        - 차원의 저주 문제

- Linear Classification
    - parametric model의 가장 단순한 형태
    - KNN에서는 parameter가 없었지만, 여기선 weights라고 불리는 파라미터가 존재
        
      <img width="514" alt="Untitled 2" src="https://user-images.githubusercontent.com/99728502/226164933-8e5d5bc1-a345-4e96-874b-9166b605fb9a.png">
        
    - 32x32x3 사이즈의 이미지를 1차원으로 쭉 펴서 3072x1 사이즈의 x로 변환해 사용
    - 결과는 각 클래스의 확률을 계산(10개)해야 하므로, 그에 맞게 가중치 행렬과 편향 벡터의 사이즈도 정해짐
    - Linear Classifier의 한계점은 한 클래스 내에 다양한 특징이 있을 수 있지만 모든것을 평균화하여 사용하기 때문에 다양한 특징이 있어도 각 카테고리를 인식하기 위한 템플릿은 하나 뿐
        - 템플릿과 유사한지를 비교한 뒤 그 템플릿과 유사한 카테고리로 분류하는데
        - 템플릿은 평균을 통해 만들기 때문에 비교하기에 부적합하다.
    - 이미지를 고차원 속의 한 점이라고 생각한다면, Linear Classifier은 각 클래스를 구분시켜주는 선형 결정경계를 그려주는 역할을 수행할 수 있음
    - 하지만 이 경우에는 이 Linear Classifier을 망칠 수 있는 경우가 많음(아래의 경우)
       <img width="525" alt="Untitled 3" src="https://user-images.githubusercontent.com/99728502/226164942-8826a0f7-e52c-48f3-9e85-a7dfd1cba464.png">
