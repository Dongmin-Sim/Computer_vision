# RCNN(Regions with CNN)

*RCNN* 은 대표적인 *Region Proposal* 기반의 *Object Detection* 모델입니다. 이는 *Two-stage Detecto*r 방식에 해당합니다. ([Two stage Dector 방식이란?](https://github.com/Dongmin-Sim/Computer_vision/blob/main/ObjectDetection/Computer_Vision(Object_Detection).md)) 



## RCNN 의 구성

*RCNN* 의 큰 단계를 나누어보면 다음과 같습니다.

![RCNN_stage1](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/RCNN/RCNN_stage1.png)

*Object* 가 있을만한 2,000개의 영역을 추천하는 *Region Proposal* 단계( *stage1* ) 와

![RCNN_stage2](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/RCNN/RCNN_stage2.png)

*Region Proposal* 된 각각의 *Region* 영역을 *CNN* 에 통과시키고 나온 *Feature map* 을 통해 *classifcation* 하고 *Bounding-Box Regression* 하는 stage 2단계로 나누어 질 수 있습니다.



**Soft Vector Machine(SVM)**은 분류를 위해 추가 되는 새로운 알고리즘로써 *softmax* 알고리즘 보다 *SVM* 을 사용했을때 *Detection* 성능이 올라갔다고 논문에서 설명했습니다. *softmax* 로 학습을 시키고 학습된 가중치로 SVM 으로 다시 학습하는 방법을 사용했다고 합니다. 

최초에는 *AlexNet*을 사용하여 학습이 진행되었습니다.  기존  *CNN* 아키텍쳐의 경우에는 반드시 동일한 이미지의 사이즈가 들어와야 했습니다. (*FC layer* 에 연결해주어야 하기 때문) 그래서 *proposal* 된 이미지의 사이즈를 맞추는 과정에서 불가피하게 이미지의 변형(crop & warp) 되는 일이 발생하게 되었고 다음과 같은 현상이 발생하였습니다.

*  *AlexNet* 이 *pre-trained* 한 *ImageNet* 은 이미지가 *warp* 나 *crop* 된 이미지가 아니었기 때문에 성능이 감소하는 일이 발생 

* 그래서 *ImageNet*의 사진들을 *wrap* 된 이미지로 *AlexNet*을 다시 *train*을 시켜 *RCNN* 을 구성

다시 정리하자면 2,000 *Region* 이 *pre-trained* 된 *AlexNet* 에 통과되면서 만들어진 *Feature map 을 바탕으로* SVM 알고리즘을 거쳐 *classification* 하고, *Bounding-Box Regression* 이 이루어지게 되는 방식으로 작동합니다. 위에서 언급한 이미지의 크기에 관한 문제점들은 후에 나올 SPPNet 에서 해결됩니다. 



## RCNN 요약

![RCNN개요](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/RCNN/RCNN%EA%B0%9C%EC%9A%94.png)

1. 이미지가 입력됩니다.

2. input 된 이미지에 대해 약 2,000개의 Region proposal 을 수행함. 

   개별 이미지를 동일한 사이즈로 wrap 시킴.

3. CNN 네트워크를 통과하면서 이미지의 feature 를 추출하게 됨. 

4. 추출된 feature 를 통해 classification 과 box regression 을 진행함. 





## Bounding Box regression

RCNN 은 최초로 인풋 이미지에 대해서 Selective Search 기법([Selective_Search 구현 확인](https://github.com/Dongmin-Sim/Computer_vision/blob/main/ObjectDetection/Selective_Search.ipynb))으로 region proposal 을 수행하게 되는데,  정확한 이미지의 region을 선택하는 것이 아닌 object 가 있을 법한 곳의 region 을 추천해주기 때문에 정확도가 떨어집니다. 그래서 이후에 별도로 box regression 을 다시 수행합니다.

![Bounding_Box_Regression.001](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/RCNN/Bounding_Box_Regression.001.png)

Box regression 은 실제 object 가 위치해 있는 Ground Truth와 predict 간의 오차를 줄여가는 업데이트를 통해 Ground Truth 에 box를 맞추는 회귀식을 세우는 것이 목표가 됩니다. 즉 다시말해 Ground Truth를 찾기 위한 선형회귀의 손실함수 output 을 최소화할 수 있는 weight 값을 찾는 것! 이라고 표현할 수 있습니다. 

그림을 참고하여, 기존의 P_x, 와 P_Y 의 좌표 값들이 Ground Truth 의 값인 G 값으로 이동하기 위한 역할을 하는 d(P) function 을 학습시키게 되면 proposal 된 P 를 G 에 맞출수 있다고 합니다.  d(P) function은 pool layer 5 의 feature 들에 학습가능한 모델파라미터의 벡터값(가중치 w)을 곱한 선형 함수라고 논문에서는 설명하고 있습니다.

결과적으로 회귀 목표값들은 그림의 t 값들로 표현이 되고, 이 t값과 d(P) function 을 이용하여 가중치 w 를 학습시킬 손실함수를 정의하고 최적화할 수 있는 방향으로 Bounding Box Regression 을 수행하게 됩니다.

![image-20210406215559510](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/RCNN/lossfunction.png)

자세한 내용은 논문의 Appendix C -  Bounding-box regression 부분을 참고



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

