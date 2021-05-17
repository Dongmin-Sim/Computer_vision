# SPP(Spatial Pyramid Pooling) Net

이전 ["RCNN"]() 에서 RCNN의 단점들에 대해 보완 및 연구가 진행되면서 RCNN 계열의 다양한 fast-RCNN, faster-RCNN 과 같이 더 향상된 알고리즘들이 나오게 된다고 언급했는데, RCNN 의 기존의 문제점들을 Spatial Pyramid Pooling layer 로 해결한 것이 SPPNet 입니다. 이후에 SPPNet 의 개선사항 및 장점들을 많은 부분 반영한 알고리즘인 fast-RCNN이 나오게 됩니다. 



## RCNN 의 주요 문제점들

RCNN을 통해 deep learning 기반의 object detection의 가능성을 확인한 많은 computer vision 연구자들이 기존의 RCNN 의 문제점들을 어떻게 개선시킬 것인가에 대한 물음을 던지기 시작했습니다. 

RCNN 계열의 알고리즘을 살펴보다보면 이러한 문제점들을 하나하나 해결해가면서 발전하는 것을 확인해볼 수 있습니다. 

RCNN 의 주요한 문제점은 다음과 같았습니다.

1. 수행시간이 오래 걸린다는 점
2. Region Proposal 을 통해 추출된 Region 영역의 이미지가 crop/wrap 되어야 한다는 점

첫번째 문제점은 개별 이미지마다 2,000개의 Region 영역의 이미지가 CNN에 입력되기 때문에(개별 이미지 마다 많은 feature map 을 생성해야 함) 발생하는 문제점이었고, 두번째는 기존의 CNN 아키텍쳐들은 입력 이미지의 사이즈가 정해져 있었는데, 이 때문에 region 영역들의 이미지를 CNN 아키텍쳐에 통과시키려면 고정된 이미지 사이즈에 맞추기 위해 이미지를 crop 또는 wrap 해야만 했습니다. 



```
📌 입력 이미지의 사이즈가 고정되어야 했던 가장 큰 이유는 CNN layer 뒤에 이어지는 FC layer가 고정된 크기의 벡터를 받기 때문입니다. 
```



이러한 문제점을 해결하기 위한 방법으로

1. 2,000개의 모든 Region Proposal 이미지를 CNN 을 통해 Feature Extraction 하지 않는다. 
2. 원본이미지만 CNN에 통과시켜 Feature Map 생성
3. Selective Search 로 추천된 영역의 이미지와 Feature Map 과 매핑하여 추출

과 같은 단계를 통해 문제를 개선하는 방향으로 나아가게 됩니다. 

하지만 이 과정에서 넘어야할 또 다른 문제가 있었는데, 그것은 위에서 언급한 두번째 문제점입니다. 3번 과정에서 추출된 값들은 1D flattened FC layer 통과해야하는데 FC layer 들은 고정된 크기의 입력을 받기 때문에 문제가 발생하게 됩니다. 왜냐하면 Selective Search 로 나온 이미지와 Feature Map 매핑된 후보 영역의 사이즈가 모두 다르기 때문에 입니다(그림 참고). 



이 문제점을 해결하는 과정에서 아래 아이디어를 바탕으로 SPPNet 이 등장하게 됩니다. 

> FC layer 에 통과되는 피쳐맵들의 사이즈가 각기 달라 문제가 된다면, FC layer 를 통과하기 전에 **각기 다른 사이즈를 가진 RegionProposal 이미지가 반영된 피쳐맵들을 동일한, 고정된 크기의 Vector로 만들어주는 layer** 를 적용시키자

사실 SPP(Spatial Pyramid Pooling) 는 image classification 에서 활용되었었던 기법으로 CNN 상에서 서로 다른 이미지의 크기를 고정된 크기로 변환하는 역할을 합니다. 

그래서 CNN 을 통과한 **각기 다른 사이즈**를 가진 RegionProposal 이미지가 반영된 피쳐맵들을 FC layer 를 **통과하기 이전에 SPPNet이 적용**되는 방식으로 위의 **문제점을 해결**하고자 하는 **시도**가 **SPPNet 의 핵심**이라고 볼 수 있습니다.



### SPPNet의 작동방식 (Spatial Pyramid Matching 기법)

Spatial Pyramid Pooling 은 Bag of Visual word 라는 고전적인 컴퓨터 비전영역의 기법의 문제점을 해결하기 위해 나왔습니다. 자연어를 처리분야에 주로 사용되는 기법으로 각 단어들에 대해 벡터화를 시키는 텍스트 기법을 유사하게 이미지 분야에 적용시킨 것인 Bag of visual words 기법입니다.

그러나 Bag of visual words 는 이미지의 위치적인 특성을 반영하지 않는다는 문제점을 갖습니다. 그렇기 때문에 이미지를 별로 각각의 분면으로 분할한 뒤 분면상의 이미지의 특징 값들을 벡터화 시키는 시도가 있었는데 바로 SPM기법 입니다.

![SPM](/Users/coding_min/home/Github/DLComputer_Vision/keynote/img/SPPNet/SPM.png)



> SPM 기법은 전제이미지를 영역의 수를 점차 늘려가며 분할하고 각 분할된 영역의 이미지마다 히스토그램을 계산, 계산된 히스토그램의 피라미드를 구축하여 유사도(?)를 계산하는 기법입니다.

 이를 그림과 함께 이해하는 것이 더 쉽습니다. 전체 이미지 영역(Level_0)에서 각 개별 feature 들의 히스토그램을 추출하고, 4분면(Level_1)의 개별분면에 대한 feature 들의 히스토그램 특성을 추출하고, 16분면(Level_2)으로 나눈 이미지의 각 분면들의 feature 히스토그램을 추출하게 됩니다.

![SPM2](/Users/coding_min/home/Github/DLComputer_Vision/keynote/img/SPPNet/SPM2.png)

그렇게 나온 각 분면별 feature 히스토그램을 벡터화 시켜서 결합을 시키면 원본이미지의 특징을 잘 나타내는 벡터가 됩니다. (Bag of visual words 보다 효과적으로 이미지 내에서 위치특성을 반영가능)

앞선 분면분할을 통해 feature 히스토그램을 벡터화 시키는 과정을 이용하면 RCNN 에서 직면했던 고정된 vector 크기를 만들어내는 것이 가능해집니다. 왜냐하면 나누어지는 분면의 수에 따라서 vector 값의 크기가 결정되기 때문입니다.
$$
vectorsize = feature수  * 분면개수
$$
예를 들어, 한 이미지내의 feature 가 3개라고 가정을 한다면, 분면을 나누지 않은 상태(Level_0)인 전체 이미지에서 각 feature 들의 히스토그램을 vector 화 시킨다면 vector 의 크기는 3으로 표현될 수 있을 것입니다. 전체 이미지를 4분면으로 분할한 상태(Level_1)에서 각 분면별로 feature 들의 히스토그램을 vector 화 시킨다면, vector 크기는 3 * 4 = 12 로 표현될 수 있습니다. 전체 이미지를 16분면으로 분할한 상태(Level_2) 에서 각 분면별로 feature 들의 히스토그램을 vector 화 시킨다면, vector 크기는 3 * 16 = 48 로 표현될 수 있습니다. 그렇다면 이 전체 이미지는 3 + 12 + 48 = 63개의 원소를 가진 vector 값으로 표현이 가능해집니다. 

이렇게 되면 입력받게 되는 feature map 의 크기가 8 * 8 이던 8 * 16 이던 이미지의 사이즈는 vector 의 크기에 영향을 받지 않게 됩니다. 앞서 RCNN 이 직면했던 고정된 vector 크기문제를 SPP layer를 통해 해결할 수 있는 것 입니다. 



### Spatial Pyramid Pooling layer

![spatial pyramid pooling](/Users/coding_min/home/Github/DLComputer_Vision/keynote/img/SPPNet/spatial pyramid pooling.png)

Spatial Pyramid Matching 기법으로 Pooling layer 를 적용한 것입니다. SPPNet은 CNN의 마지막 Pooling layer 로 SPP 로 대체하여 Feature map 분할 및 max pooling 을 이용하여 고정된 vector 크기를 만들어 냅니다.



![SPPVector](/Users/coding_min/home/Github/DLComputer_Vision/keynote/img/SPPNet/SPPVector.png)

 다음 이미지를 보면 SPP 레이어를 이용하여 8 x 8 Feature Map 에 대하여 각각의 분할된 영역에서 Max Pooling 이 적용되어 고정된 사이즈의 Vector 값이 추출되는 것을 확인할 수 있고, 8 x 12 Feature Map 에 대해서도 동일하게 분할된 영역에서 Max Pooling 이 적용되어 고정된 사이즈의 Vector 값이 추출됩니다. 

 이러한 SPP 레이어의 특성 덕분에 각기 다른 사이즈를 가진 *Region Proposal* 이미지가 반영된 피쳐맵들을 동일한, 고정된 사이즈의 *Vector* 값으로 변환이 가능해졌고, 그 결과로 *FC layer* 에 입력되는 이미지 사이즈 문제를 해결할 수 있었습니다. (*image crop/warp* 문제 해결)



### SPPNet에서 이미지 classification 





### SPPNet 의 장점과 한계점





### 느낀점







### 참고

[딥러닝 컴퓨터 비전 가이드]()

[갈아먹는 머신러닝](https://yeomko.tistory.com/14)