# Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks



## Abstract

State-of-the-art 객체 탐지 네크워크는 객체의 위치를 가정하는 region proposal algorithms에 의존한다. SPPNet이나 Fast-RCNN과 같은 advanced 네트워크들은 region proposal 연산을 bottleneck으로 드러내놓으면서 이러한 탐지 네트워크의 실행 시간을 줄였다.  본 연구에서는, detection network와 풀-이미지 컨볼루션 피쳐를 공유하는 방식을 통해 region-proposal를 계산하는데 있어 비용이 거의 들지 않는 Region Proposal Network(RPN)을 소개한다. RPN은 Fast R-CNN에서 객체 탐지를 위해 사용되었던 고품질의 region-proposal을 생성하기 위해 end-to-end 방식으로 훈련된다. 본 저자들은 더 나아가, RPN과 Fast R-CNN을 컨볼루션 피쳐를 공유하는 single network로 통합하였다.(최근 인기를 끌고 있는 뉴럴네트워크 terminology 인 "attention" 메커니즘을 사용하여,  RPN의 구성요소는 통한된 네트워크가 어느곳을 봐야하는지 알려준다.) 매우 깊은 VGG-16 모델의 경우, 본 연구의 detection 네트워크(시스템)은 이미지당 300개의 proposal만으로 PASCAL VOC 2007, 2012 및 MS COCO 데이터 세트에서 State-of-the-art  객체 감지 정확도를 달성하면서 GPU에서 5fps(including all steps)의 프레임률을 가진다. ILSVRC와 COCO 2015 대회에서, Faster R-CNN과 RPN은 여러 트랙에서 1위를 차지한 참가 모델들의 기초가 되었다. 코드는 공개적으로 이용 가능하다.



**Index Terms**-Object Detection, Region Proposal, Convolutional Neural Network.



## Introduction

Object Detection의 최근 발전은 region-proposal 방법과 지역 기반 컨볼루션 신경망(R-CNN)의 성공으로 이끌어져왔다. 지역 기반 CNN은 [[RCNN 논문]](https://arxiv.org/pdf/1311.2524.pdf)에서 처음 개발된 것처럼 계산 비용이 많이 들었지만, proposal 전반에 걸친 컨볼루션 공유 덕분에 비용이 대폭 절감되었다. [[SPPNet 논문]](https://arxiv.org/pdf/1406.4729.pdf), [[Fast R-CNN 논문]](https://arxiv.org/abs/1504.08083) 가장 최근 화두인 Fast R-CNN은 region-proposal에 소요된 시간을 제외했을 때는 very deep network를 사용하여 거의 실시간 속도를 달성한다. 이제 proposal은 State-of-the-art detection 네트워크(시스템)에서 테스트 시간 연산의 "bttleneck"(병목)이라고 볼 수 있다.

