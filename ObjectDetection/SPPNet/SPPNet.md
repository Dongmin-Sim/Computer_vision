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

그래서 CNN 을 통과한 각기 다른 사이즈를 가진 RegionProposal 이미지가 반영된 피쳐맵들을 FC layer 를 통과하기 이전에 SPPNet 가 적용되는 방식으로 위의 문제점을 해결하고자 하는 시도가 SPPNet 의 핵심이라고 볼 수 있습니다.

### SPPNet의 작동방식 (Spatial Pyramid Matching)

Spatial Pyramid Pooling 은 Bag of Visual word 라는 고전적인 컴퓨터 비전영역의 기법의 문제점을 해결하기 위해 나왔습니다. 자연어를 처리분야에 주로 사용되는 기법으로 각 단어들에 대해 벡터화를 시키는 텍스트 기법을 유사하게 이미지 분야에 적용시킨 것인 Bag of visual words 기법입니다.

그러나 Bag of visual words 는 이미지의 위치적인 특성을 반영하지 않는다는 문제점을 갖습니다. 그렇기 때문에 이미지 별로 각각의 분면을 나눈 뒤 분면상의 이미지의 특징 갑들을 벡터화 시키는 것이 SPM 입니다. 







### 참고

[딥러닝 컴퓨터 비전 가이드]()

[갈아먹는 머신러닝](https://yeomko.tistory.com/14)