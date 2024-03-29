# Lecture 11

![Untitled](https://github.com/3umin/Study/assets/99728502/358069f3-c064-4318-a730-130e33ffe581)

오늘 다룰 Task들

# Semantic Segmentation

![Untitled 1](https://github.com/3umin/Study/assets/99728502/f82aa19e-7727-4512-9baf-d7475aeefec8)

- 이미지를 입력받아 이미지의 모든 픽셀에 카테고리를 지정하는 Task
- 개별 객체를 구분하는 것이 아니라, 픽셀이 어떤 카테고리에 속하는지만 보여줌(즉 암소 두마리를 구별할 수 없다, 암소라고 레이블링 된 픽셀 덩어리만 얻음) → 이 문제는 Instance Segmentation이 해결
- Classification의 일종이기 때문에, 무슨 무슨 클래스가 있는지에 대한 정보는 주어짐

- Semantic Segmentation을 위해 입력 이미지를 아주 작은 단위로 분류하여 Classification을 수행하는 방법을 고려해볼 수 있음
    - 하지만 이 방법은 매우 비효율적이다!
    - 이미지의 작은 영역을 계속 분류하기 때문에 비슷한 특성을 공유하는 것이 아니라, 계속 새로 연산을 수행함
    
    ![Untitled 2](https://github.com/3umin/Study/assets/99728502/cde46397-38bb-4bd1-9a2d-a81a73c7bd22)


### Fully Convolutional Network

- 이미지 영역을 나누고 독립적으로 분류하지 않고, FC Layer가 없이 Convolutional Layer로만 구성 → 이미지의 공간정보가 손실되지 않음
- 출력 Tensor는 C x H x W 사이즈인데, 여기서 C는 카테고리의 수를 의미
- 즉 모든 픽셀 값에 대해 Classification Score를 매기게 됨
- 이 네트워크의 경우는 입력 이미지의 Spatial Size를 계속 유지해야 하기 때문에, 연산하는 비용이 너무 크다…
    
    ![Untitled 3](https://github.com/3umin/Study/assets/99728502/88e9f625-2897-46a6-a87e-990da650b683)

- 이를 해결하기 위해 원본 이미지를 Downsampling - Upsampling 하는 과정을 거친 모델을 사용하기도 함
- 근데 Pooling Layer, Stride 조절 등으로 Downsampling 하는건 해봤지만, Upsampling은 이번이 처음 → 어떻게 할까?
- “Unpooling”
    
    ![Untitled 4](https://github.com/3umin/Study/assets/99728502/ed7c0ddf-7289-4af2-af51-90f54b745e0f)

    - Nearest Neighbor 기법 : 각각의 픽셀에 해당하는 확장된 영역을 픽셀 값으로 고정
    - Bed of Nails 기법 : 한 위치에만 값을 넣고 나머지 픽셀은 0으로 지정
        
        ![Untitled 5](https://github.com/3umin/Study/assets/99728502/c900cb04-cb5d-4337-8db7-b629b1993bb6)

    - Max Unpooling : Max Pooling 처럼 원본의 픽셀 값이 영역하에 Max 값이 되도록 지정(나머지 픽셀값은 0으로 넣음) → Bed of Nails와 유사하지만, Bed of Nails는 같은 위치에만 값을 넣는 반면 Max Unpooling은 이전 Max Pooling 층에서의 Max 값의 위치를 기억해 해당 위치에 값을 삽입
        
        ![Untitled 6](https://github.com/3umin/Study/assets/99728502/a5904e02-1f20-4e7a-8944-ecc7bcd96e16)

    - Transpose Convolution(Deconvolution, Upconvolution, Backward strided convolution 등등) : Input Feature map 하나의 값(스칼라)와 필터와 곱해 출력의 n x 지역의 Feature map에 값을 지정(값이 중첩되면 값끼리 더해줌)

# Classification + Localization

<img width="98" alt="Untitled 7" src="https://github.com/3umin/Study/assets/99728502/79d06ccd-cc05-46ed-8b5d-bb987920d4b6">

- Image Classification에 더해 그 객체가 어디에 있는지 박스로 영역을 구분하는 Task
- Localization에서는 Object Detection과 다르게 이미지 내에서 관심있는 객체가 오직 하나 뿐이라고 가정
- FC Layer로 Class Score를 반환하는 Layer와 함께, Bounding Box의 위치를 나타내는 좌표(x, y, w, h)를 반환하는 layer도 존재 → Regression Problem!
- Class Classification → Softmax Loss / Box Coordinates → L2 Loss 로 2가지의 Loss가 존재
- Q. 오분류된 클래스의 Bbox는 어떻게 Loss를 계산하냐?
    - Bbox를 하나만 예측하지 않고 카테고리마다 하나씩 예측.
    - 그런 뒤 Ground Truth 카테고리의 Bbox와의 연산을 통해 Loss를 계산

- Q. 두 개의 Loss를 계산하면 서로 단위가 달라서 Gradient 계산에 문제가 생기는 것 아닌가?
    - Loss가 두 개이기 때문에 미분 값도 두개임
    - 실제로는 두 Loss의 가중치를 조절하는 하이퍼파라미터가 존재해 두 Loss의 가중치 합을 최종 Loss로 놓고 계산
    - 이 가중치 하이퍼파라미터의 값이 바뀌면 Loss가 아예 바뀌어버리기 때문에 정하기가 아주 까다롭다
    - 그래서 Multi-task 모델은 Loss 값으로 비교하는 것이 아니라 다른 성능 지표를 이용해 모델을 비교하는 것이 좋음

![Untitled 8](https://github.com/3umin/Study/assets/99728502/18cab135-3912-4af9-868d-892910055206)

- 이러한 Task에 더 나아가서, Human Pose Estimation 같이 더 다양한 Bounding이 필요한 문제로도 확장
    - 이 모델의 경우 14개의 관절포인트를 예측하기 때문에 (x, y)로 구성된 14개의 좌표값이 출력되고, 14개의 점에 대해 Regression loss를 계산한 뒤 학습

# Object Detection

<img width="98" alt="Untitled 9" src="https://github.com/3umin/Study/assets/99728502/1de66ef3-7b9e-4524-af40-31fad6d34f74">

- CV의 가장 핵심 영역 중 하나
- 한 이미지 안에 들어있는 여러 객체와 객체 영역을 인식하는 Task
- 마찬가지로 한정된 카테고리 갯수만 생각
- Object Detection은 Classification + Localization과 다르게 이미지마다 몇 개의 Output을 뱉어야 하는지가 다르기 때문에, 일반적인 회귀 문제로는 해결하기 어렵다 → 새로운 방법 필요
- Sliding Window
    - 작은 영역으로 쪼개어 CNN을 거쳐 작은 영역의 Classification을 수행 (배경 카테고리도 있음)
    - 이 방법의 문제점은 어떻게 영역을 추출하느냐. Object의 크기도 모르기 때문에 sliding window의 경우의 수가 너무 많다. 또한 계산량이 너무 많다.

### Region Proposals

![Untitled 10](https://github.com/3umin/Study/assets/99728502/45027a0d-2e80-4032-adad-27d86f7a84a5)

- Object가 있을법한 여러개의 Bbox를 제공받은 뒤 Classification을 수행하는 방법
- Object가 있을법한 영역은 이미지 내에 뭉텅진(blobby)한 곳을 찾는 방법으로 수행
- 하지만 노이즈가 너무 심하다.

### R-CNN

![Untitled 11](https://github.com/3umin/Study/assets/99728502/30039f45-da69-4a07-8dd1-db6250c73a7a)

- 이미지가 주어지면 Region Proposal을 얻기 위해 Region Proposal model을 통과
- CNN의 입력으로 사용하기 위해 추출된 Region Proposal을 고정된 사이즈로 크기를 변경
- 그 후 각각의 Proposal을 CNN에 통과
- RCNN은 Bbox의 카테고리와, Bbox를 보정해줄 offset 값 4개도 함께 예측
- 문제점
    - 계산 비용이 여전히 높다(Region proposal이 독립적으로 CNN 연산을 하기 때문에)
    - 그렇기 때문에 학습 과정도 상당히 느림
    - Test time도 아주 느리다

### Fast R-CNN

![Untitled 12](https://github.com/3umin/Study/assets/99728502/f8460745-89d9-488e-ad4c-058dfa561009)

- 각 ROI마다 CNN을 수행하는 것이 아니라 전체 이미지에 CNN을 수행
- 여전히 Selective한 방식으로 Region Proposal을 뽑지만, Input 이미지에서 이를 뽑는게 아니라 CNN을 거친 Feature map에서 Proposal을 뽑음 → 훨씬 효율적
- Feature map에서 Proposal을 거친 뒤 FC Layer에 입력되어 예측(FC Layer의 크기에 맞게 조정해줘야 하는데, 이를 ROI pooling layer가 수행)

![Untitled 13](https://github.com/3umin/Study/assets/99728502/cded63ef-c149-4f21-bab5-ade481d9a5c9)

- 다른 모델보다 훨씬 빠르다!
- 하지만 문제점은 Run time이 region proposal을 계산하는데에 거의 다 쓰이기 때문에 이 Region Proposal을 계산하는 구간이 병목구간이다 → 이러한 문제는 Fast**er** R-CNN이 해결

### Faster R-CNN

![Untitled 14](https://github.com/3umin/Study/assets/99728502/179bb65d-5d24-47dd-a5a0-df2e222aaacd)

- 네트워크가 Region Proposal을 직접 계산
1. Fast R-CNN처럼 입력이미지 전체가 전체 네트워크로 들어가서 Feature map을 만듬
2. 별도의 Region Proposal Network를 통과해 Region Proposal을 예측
3. Fast R-CNN과 동일하게 진행
- 훨씬 더 빠르다! (CNN이 Proposal을 하도록 만든 점이 차이점)

### YOLO / SSD

![Untitled 15](https://github.com/3umin/Study/assets/99728502/70c0beb7-2d30-4f2f-ab09-99bdae11a1c7)

- 각 Task를 따로 계산하는 것이 아니라 하나의 Regression 문제로 해결하는 접근법
- 입력 이미지를 큼지막한 Grid로 나누고, Grid Cell 내부에 Base Bbox를 지정해둔 뒤, 이를 기반으로 Bbox의 offset을 예측하며 각 Bbox에 대해 Classification Score을 계산하는 원리
- Output으로는 7 x 7 x (5B + C)
    - 7 x 7 : Grid Size
    - B : Base bbox의 개수, bbox의 offset (4개)와 Confidence score (1개)로 구성
    - C : Classification Score
- 이를 거대한 CNN으로 학습

### Dense Captioning

![Untitled 16](https://github.com/3umin/Study/assets/99728502/90e9002c-0e9e-4415-9db7-e381fec3f4ad)

- 각 Region에 대해 카테고리를 예측하는게 아니라, 각 Region에 대해 Caption을 예측해야함
- Captioning을 위해 RNN Language Model을 사용

# Instance Segmentation

<img width="99" alt="Untitled 17" src="https://github.com/3umin/Study/assets/99728502/3db899eb-0d1c-4117-8cbb-775d4205cfd9">

- 입력 이미지가 주어지면 객체별로 객체의 위치를 알아내야 하고, 객체별 Segmentation Mask를 예측해야함(어떤 픽셀이 객체에 해당하는지 확인)
- Semantic Segmentation과 Object Detection을 합친 문제

## Mask R-CNN

![Untitled 18](https://github.com/3umin/Study/assets/99728502/4cd1d97e-bc43-4cdb-b4cb-417592babb58)

- Faster R-CNN과 동일하게 처음 입력 이미지가 CNN을 거쳐서 Feature map을 추출한 뒤 Feature map에서 RPN(Region Proposal Network)의 ROI를 뜯어낸 뒤 각 Bbox마다 Segmentation mask를 예측하도록 학습
- RPN으로 뽑은 ROI 영역 내에서 각각 Sementic Segmentation을 수행
- Class와 BBox Proposal의 좌표를 보정하는 Bbox Regression을 예측

![Untitled 19](https://github.com/3umin/Study/assets/99728502/65c212aa-7da3-4e43-afc5-9343a200fa1a)

- Mask R-CNN으로 Pose Estimation도 가능하다!
