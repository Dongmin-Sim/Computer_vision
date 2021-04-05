# RCNN(Regions with CNN)

RCNN 은 대표적인 Region Proposal 기반의 Object Detection 모델입니다. 이는 Two-stage Detector 방식에 해당합니다. ([Two stage Dector 방식이란?](https://github.com/Dongmin-Sim/Computer_vision/blob/main/ObjectDetection/Computer_Vision(Object_Detection).md))



(stage1)

2,000개의 Region 영역을 Proposal 하는 Region Proposal 단계

(stage2)

Region Proposal 각 Region 영역을 CNN 에 통과시키고 나온 Feature map 을 통해 classifcation 하고 Bounding-Box Regression 하는 단계

soft vector machine은 분류를 위해 추가 되는 새로운 알고리즘, softmax 알고리즘 보다 SVM 을 사용했을때 Detection 성능이 올라갔다고 논문에서 설명함. softmax 로 학습을 시키고 학습된 가중치로 SVM 으로 다시 학습하는 방법을 사용했다고 함. 

최초에는 AlexNet을 사용하여 학습이 진행됨.  CNN 인 경우에는 반드시 동일한 이미지의 사이즈가 들어와야함. AlexNet(227 * 227 -> 6 * 6, channel:256) 

그래서 proposal 된 이미지를 227*227 로 맞추는 과정에서 이미지의 비율이 유지가 되지 않고 사진이 crop 되거나 찌그러지는 현상이 적용되게 됨. (image crop & warp 적용) 

*  AlexNet 이 pre-trained 한 ImageNet 은 이미지가 warp나 crop 된 이미지가 아니었기 때문에 성능이 감소하는 일이 발생되곤 했음. 

* 그래서 ImageNet의 사진들을 wrap 된 이미지로 AlexNet을 다시 train을 시켜 RCNN 을 구성함. 

이러한 이미지들이 ImageNet 으로 pre-trained 된 AlexNet 에 통과되면서 SVM 알고리즘을 거쳐 classification 이 이루어지게 됨. 



## RCNN 개요 

![RCNN개요](/Users/coding_min/home/Github/DLComputer_Vision/keynote/img/RCNN/RCNN개요.png)

1. 이미지가 입력됩니다.

2. input 된 이미지에 대해 약 2,000개의 Region proposal 을 수행함. 

   개별 이미지를 동일한 사이즈로 wrap 시킴.

3. CNN 네트워크를 통과하면서 이미지의 feature 를 추출하게 됨. 

4. 추출된 feature 를 통해 classification 과 box regression 을 진행함. 





## Bounding Box regression

RCNN 은 최초로 인풋 이미지에 대해서 Selective Search 기법([Selective_Search 구현 확인](https://github.com/Dongmin-Sim/Computer_vision/blob/main/ObjectDetection/Selective_Search.ipynb))으로 region proposal 을 수행하게 되는데,  정확한 이미지의 region을 선택하는 것이 아닌 object 가 있을 법한 곳의 region 을 추천해주기 때문에 정확도가 떨어짐. 그래서 이후에 box regression 을 다시 수행해줌



Box regression 은 실제 object 가 위치해 있는 Ground Truth와 predict 간의 오차를 줄여가는 업데이트를 통해 Ground Truth 를 찾아주는 회귀식을 세우는 것이 목표가 됨. 즉 다시말해 Ground Truth와 predict의 손실함수를 output 을 최소화할 수 있는 weight 값을 찾는 것! 이라고 표현할 수 있음. 



## RCNN 성능 비교

VOC 2010 데이터 세트에 대한 성능 결과 기존 타 모델들에 비해 mAP 10% 이상 향상된 결과를 보여줬음

[mAP 란 ?]()

![image-20210405190553963](/Users/coding_min/Library/Application Support/typora-user-images/image-20210405190553963.png)

BB = Bounding Box regression



## RCNN 의 장단점 

장점

* 높은 정확도 (동시대의 타 알고리즘에 비해 매우 높은 Detection 정확도를 보여줌)
* 이로 인해 딥러닝을 활용한 image Detection 의 방향성을 제시해줌



단점

* 매우 느린 Detection 시간
  * 개별 이미지마다 2,000 개의 region 을 proposal 하고, 2,000 개의 region 에 대해 CNN feature map 을 생성하기 때문에 시간적인 측면에 있어서 매우 효율적이지 못함.
* 학습 프로세스와 네트워크 구성의 복잡함
  * Selective Search, CNN extractor, Bounding Box Regressor, SVM 각각 구성되어 있어 네트워크의 구조가 복잡하고 이러한 과정을 거쳐서 학습 및 Detection 되는 구조임. 



RCNN 의 높은 정확도(동시대 대비)라는 장점은 Deep learning 기반의 Detection 의 성능과 가능성을 확인하는 계기가 되었고 기존의 Sliding window 보다 Region Proposal 기반의 Detection의 성능과 가능성도 입증하는 계기가 되었습니다. RCNN 의 단점들은 수행시간을 줄이고, 복잡하게 따로 분리되어 있는 구조를 통합하는 방향으로 추후 연구의 방향성을 제시해주는 계기가 되었습니다. 그 결과로 RCNN 이후로 RCNN 계열의 다양한, 발전된 알고리즘들이 나오게 됩니다. 

