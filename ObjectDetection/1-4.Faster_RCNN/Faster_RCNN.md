## Fast R-CNN 의 한계

* Selective Seacrch도 좋은 기법이지만, CPU 기반의 느린 수행 속도로 인해 real-time task에 대해 적합한 방법은 아니었음. 
* 완전한 end-to-end 모델이라고 볼 수 없었음. 



## Faster R-CNN 개요

RCNN 계열 모델들 중 가장 뛰어난 성능을 가지고 있음. 기존의 Fast RCNN + RPN 이 결합된 모델이 Faster RCNN 이라고 할 수 있음. 기존에 Selective search 가 하던 역할을 RPN이 대체하여 기존의 CPU 기반의 수행방식을 가지고 있던 것을 GPU 기반의 Deep Learning Network로 변경하였으며 이를 통해 end-to-end 네트워크 구조를 실현하였음. (SVM, selective search와 같은 외부 모듈 x)



## Anchor Box

기존에 Selective Search 는 이미지에 대해 edge와 color 값의 변화를 추적을 근거로하여 object 를 proposal하는  알고리즘이 있었으나, 

주어지는 데이터는 feature map의 pixel 과 bounding box뿐인데 어떻게 object, region proposal 할 것인가? 

이를 해결하기 위한 해답은 Anchor Box



Anchor Box란 Object 가 있는지 없는지에 대한 후보 Box 를 의미한다. Anchor Box 가 속하는 영역에 object 가 있는지 없는지 Ground Truth Bounding Box 를 통해 네트워크를 학습함. 



총 9개의 Anchor Box로 구성되며 3개의 서로 다른 크기를 갖음, 3개 모두 서로 다른 ratio 로 구성



[Anchor box img]



Ground Truth Bounding Box 의 겹치는 IOU 값에 따라 Positive, Negative Anchor Box로 분류



## RPN(Region Proposal Network)





### Classification, Bounding Box Regression



## RPN Loss function


$$
L(\{p_i\}, \{t_i\}) = \dfrac{1}{N_{cls}} \sum_iL_{cls}(p_i, p^*_i) + \dfrac{\lambda}{N_{box}}\sum_{i}p^*_i \cdot L_{1}^{smooth}(t_{i}, t^{*}_{i})
$$
RPN의 손실함수는 다음과 같이 정의가 되고 총 두가지의 loss 값을 계산하는 부분으로 나뉘게 됨.



### Classification loss

$$
\dfrac{1}{N_{cls}} \sum_iL_{cls}(p_i, p^*_i)
$$

$N_{cls}$ : 미니 배치에 따른 정규화 값(255)

$p_{i}$ : Anchor i가 object 일 확률 (softmax 값 FG인지 BG인지)

$p^{*}_{i}$ : Anchor i의 Ground truth object 여부(positive-1, Negative-0)





### Bounding Box Regression loss

$$
\dfrac{\lambda}{N_{box}}\sum_{i}p^*_i \cdot L_{1}^{smooth}(t_{i}, t^{*}_{i})
$$

$N_{box}$ : 박스 개수 정규화 값(최대 2400=40*60)

$t_{i}$ : Anchor i에 대한 예측 좌표 4개(x, y, w, h)

$t^{*}_{i}$ : Anchor i에 대한 예측 좌표 4개(x, y, w, h)

$\lambda$ : 밸런싱 값 (10)





## Objectness Score

예측 Box 가 Object 일 확률(Softmax값) * GT 와의 IOU value



Object Score 가 높은 순으로 Region Proposal Box 추출





## Faster RCNN 