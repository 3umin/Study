# Lecture 4

## Computational Graph

![Untitled](Lecture%204%2034b08566355b433b9b5a1fcc663c9636/Untitled.png)

- 그래프를 이용해 함수를 표현하는 방법
- 이 방법을 이용해서 Backpropagation(역전파)를 사용 가능
    - Backpropagation : Gradient를 얻기 위해 Computational Graph 내부의 모든 변수에 대해 chain rule을 사용

# Backpropagation

![Untitled](Lecture%204%2034b08566355b433b9b5a1fcc663c9636/Untitled%201.png)

- 출력 값인 f에 대한 x, y, z의 Gradient를 계산해야 함
- 위의 경우
${\partial f \over \partial z} = 1 \\ {\partial f \over \partial x} = {\partial f \over \partial q} * {\partial q \over \partial x} = z = -4  \\ {\partial f \over \partial y} = {\partial f \over \partial q} * {\partial q \over \partial y} = z = -4$

![Untitled](Lecture%204%2034b08566355b433b9b5a1fcc663c9636/Untitled%202.png)

- 또 다른 예시 중 하나.
- 위에 써진 값은 실제 값을 의미하고, 아래 써진 값은 각 변수로 f를 미분한 값을 의미
- 실제로 Chain Rule을 사용해 미분을 진행해나가다보면 같은 결과값을 얻을 수 있음
- Sigmoid Function의 미분식을 안다면, 파란색으로 박스쳐진 부분을 손쉽게 건너뛸 수 있음
    
    ![Untitled](Lecture%204%2034b08566355b433b9b5a1fcc663c9636/Untitled%203.png)
    

- Backpropagation에 필요한 특징
    - Max gate는 통과되는 하나의 값은 Gradient로 전체값이, 다른 하나에는 0의 Gradient가 지정되게 됨
    - Mul Gate(곱셈 게이트)는 변수에 곱해지는 다른 변수의 값으로 Gradient가 지정됨

- Vectorized Data
    - Input 값이 스칼라가 아니라 벡터인 경우에도 동일하게 적용. 단 Gradient가 Jacobian matrix로 변경
    - 만약 4096 차원의 input을 받아 4096 차원의 output을 내보내는 함수의 경우, Jacobian Matrix는 4096 x 4096 차원을 가지게 됨 → 너무 거대해서 실용적이지 않음
    - 실제로는 거대한 Jacobian을 모두 계산할 필요가 없음.
    - 요소별로 영향을 주는 정도를 알고싶기 때문에, 실제로는 Jacobian Matrix의 대각원소만 뽑아낸 대각행렬만 필요.

- 벡터에 대해 Backpropagation 하는 예시
    
    ![Untitled](Lecture%204%2034b08566355b433b9b5a1fcc663c9636/Untitled%204.png)
    
1. *에서 L2로 가는 값은 $q_i^2$을 통해 계산되므로 Gradient는 $2q_i$의 벡터로 계산됨
2. W에서 *로 가는 길은 $W \cdot x, \ {\partial f \over \partial W_{i, j}} = {\partial q_i \over \partial W_{i,j}} * {\partial f \over \partial q_i} = 2q_i x_j$를 통해 계산
    - 0.2 x 0.44 = 0.088
    - 0.2 x 0.52 = 0.104
    - 0.4 x 0.44 = 0.176
    - 0.4 x 0.52 = 0.208
    - 여기서 $[2q_ix_j] = q \cdot x^T$, Wx에서 W에 대해 미분했기 때문에 Transpose 되어 x가 뒤에서 곱해짐
3. x에서 *로 가는 길 역시 ${\partial f \over \partial x_i} = {\partial f \over \partial q_i} * {\partial q_i \over \partial x_i} = \sum_j 2q_jW_{i,j}$
    - 0.44 x 0.1 + 0.52 x (-0.3) = -0.112
    - 0.44 x 0.5 + 0.52 x 0.8 = 0.636
    - 여기서 $[\sum_j 2q_jW_{i,j}]_i = W \cdot q$, 즉 ax를 미분하면 a가 나오는 것과 같은 원리로 미분된 것.

- 코드
    
    ```python
    class ComputationalGraph(object):
    	# ...
    	def forward(inputs):
    		for gate in self.graph.nodes_topologically_sorted():
    			gare.forward()
    		return loss
    	
    	def backward():
    		for gate in reversed(self.graph.nodes_topologically_sorted():
    			gate.backward()
    		return inputs_gradients
    ```
    
    코드로 구현하면 다음과 같은 원리
    

- 신경망은 함수들의 집합(Class), 비선형의 복잡한 함수를 위해 간단한 함수들을 계층적으로 여러개 쌓아올림
- 다양한 Activation Function 사용
    
    ![Untitled](Lecture%204%2034b08566355b433b9b5a1fcc663c9636/Untitled%205.png)