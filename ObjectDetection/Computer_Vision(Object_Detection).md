# Computer Vision : Object Detection에 대해서 

Computer Vision 분야에서 다루는 주요한 task 들은 크게 4가지로 나누어질 수 있는데, 이는 다음과 같음.



* Classification (single object) : 하나의 객체에 대해서 분류하는 문제
* Localization (single object) : 하나의 객체의 위치를 bounding box 를 통해 찾고, 클래스를 분류하는 문제
* Detection (multiple objects) : 여러개의 객체에 대해서 Localization 하는 문제
* Segmentation (multiple objects) : Detection과 비슷하지만 여러개의 객체에 대해서 bounding box 보다 정교하게 객체의 실제 영역을 찾아 분류하는 문제



Objects의 위치를 찾아낸다는 기준으로 위의 task 들을 나누자면 Classificaion / Localization, Detection, Segmentation 으로 나누어질 수 있고,  Classification 은 하나의 object에 대해 이미지를 분류하는 task라고 한다면, Detection은 Localization 보다는 많은 Object 를 찾아내야한다는 점에서 조금 더 어려운 task가 될 수 있고, Segmentation 은 Detection에 비해 픽셀단위의 detection(각 픽셀이 어떤 object에 속해있는지 + 각 필셀이 속해있는 object의 class 가 무엇인지)으로 보다 정교하다는 task라고 설명할 수 있음. 

Localization/Detection 문제는 다시 두 가지의 문제로 볼 수 있는데, 하나는 수많은 features 들을 통해 **bounding** **box** **regression** 하는 문제와 이를 통해 예측된 box 값의 좌표에 해당하는 object 를 **Classification** 하는 문제가 합쳐져 있음. 

![Object_Detection_milestone](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Object_Detection_milestone.png)

[이미지 출처 : Object Detection in 20 Years: A Survey](https://arxiv.org/pdf/1905.05055.pdf)

위의 이미지는 Object Detection in 20 Years: A Survey 라는 논문에서 Object Detection의 역사에 대해 큰 획을 그은 여러 이정표들을 그래프로 시각화한 것이다. 2012년 AlexNet 을 기점으로 Traditional Detection Methods 과 Deep Learning 기반의 Detection Methods 으로 구분하게 됨. Deep Learning 기반의 Detection 방법론의 가능성이 확인되면서 이를 기반으로 다시 One-State Detector 와 Two-Stage Detector 로 나뉘어서 발전하게 됨. 



## Object Detection Algorithms

### Two-stage Detector

*One-Stage Detector* 과  *Two-Stage Detector* 의 차이점은 간단하게 구분하자면 **Two-Stage Detector**는 Region proposal 의 프로세스와 Classifciation + bounding box regressing 하는 프로세스로 나뉘어 순차적으로 진행되는 방법임. 

![Two-stage Detector](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Two-stage%20Detector.png)

먼저 *Selective Search* 나 *Region Proposal Network* (딥러닝 기반 영역제안 네트워크) 를 통해 영역을 제안한 후 후보영역군에 대한 *Classification* + Regression 을 통해 *Object Detection* 을 하는 방식으로 이루어집니다. 이러한 *Two-Stage Detector* 높은 정확도를 제공하지만 *One-stage Detector* 보다는 비교적 처리속도가 느리다는 단점이 있습니다.

* Two-Stage Detector : RCNN, SPPNet, Fast RCNN, Faster RCNN, Pyramid Networks 의 알고리즘이 해당



### One-stage Detector

앞선  *Two-Stage Detector* 의 처리속도가 비교적 느리다는 단점과 더불어 실시간으로 Object Detection 을 해야하는 요구 증가와 시대적 변화를 충족시키기엔 RCNN 은 어렵다는 분위기가 자리잡게 되면서 이러한 문제를 해결하기 위한 *one-stage detector* 방법론이 대두되게 됨.

![One-stage Detector](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/One-stage%20Detector.png)

  **One-Stage Detector** 은 대표적인 YOLO 알고리즘의 이름을 보면 직관적으로 이해 되는데 You Only Look Once(한번만 보면 돼!) 라는 이름처럼 Two-Stage 에서 하던 프로세스를 한번에 수행하게 되므로써 수행속도가 빠르다는 장점 때문에 실시간 탐지를 요구하는 어플리케이션에 주로 활용되곤 함.

보통 원본이미지를 동일한 그리드로 나누어 각 그리드의 중앙을 중심으로 지정된 bounding box 의 개수를 예측하고, 신뢰도를 계산한 후 높은 신뢰도를 가진 위치를 선택해 *Object Detection* 을 하는 방식으로 이루어집니다.

* One-Stage Detector : YOLO(you only look once), SDD, Retina-Net 의 알고리즘이 해당





## Object Detection시 당면하게 되는 문제

다른말로 하면 Object Detection 이 풀어나가야 할 문제라고도 말할 수 있는데  우리가 실생활에서 접하는 이미지는 보통 다양한 크기와 유형의 오브젝트가 섞여 있는 경우가 많음. 기본적으로 이미지에서 여러개의 Object의 위치를 찾으면서 Classification 해야하는데 Object 마다 크기와 종횡비가 다르고, 생김새가 다양한 object 가 있는 이미지에서 이들을 각각 detection해야하는 문제가 존재함. 또한 실시간 영상기반에서 Detect 해야하는 요구사항(IOT, 자율주행 등)이 점차 증가됨에 따라 Detection 하는데 걸리는 시간 또한 중요한 요소로 꼽히며 이를 위한 훈련 가능한 데이터세트를 생성해야하는 어려움이 존재함. 

- **classification + Regression 을 동시에 해야함 (알고리즘으로 극복가능)**

  이미지에서 여러개의 물체를 classification 함과 동시에 위치를 찾아야하는 어려움

- **다양한 크기와 유형의 오브젝트가 섞여 있음 (알고리즘으로 극복가능)**

  크기가 서로 다르고, 생김새가 다양한 오브젝트가 섞여 있는 이미지에서 detect 해야하는 어려움

- **중요한 Detect 시간 (알고리즘으로 극복가능)**

  Detect 시간이 중요한 실시간 영상 기반에서 Detect 해야하는 요구사항 증대

- **명확하지 않은 이미지 (알고리즘으로 극복가능)**

  오브젝트 이미지가 명확하지 않은 경우가 많음. 또한 전체 이미지에서 Detect할 오브젝트가 차지하는 비중이 높지 않음(배경이 대부분이 많음)

- **훈련 데이터 세트의 부족**

  훈련 가능한 데이터 세트가 부족함 annotation 을 만들어야 하므로 훈련 데이터 세트를 생성하기가 상대적으로 어려움.  (MS coco datatset 80개, Google Open Image 500개)



## Object Detection의 주요 구성요소

- Region Proposal : 영역 추정
- Feature Extraction & Network Prediction : Detection 을 위한 Deep Learning 네트윅 구성
- IOU, NMS, mAP(성능지표), Anchor Box : Detection 을 구성하는 기타 요소



## Sliding window 방식

![Sliding Window](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Sliding%20Window.png)

object detection의 초기 기법으로 활용됨. window를 왼쪽 상단에서 오른쪽 하단으로 이동시키며 detection 하는 방식임.

1. 다양한 형태의 window를 각각 sliding 시키는 방식
2. window scale은 고정하고 scale을 변경한 여러 이미지를 사용하는 방식(image의 scale을 조정) 이 있음

Object는 작고 배경이 큰 경우 detection의 낭비가 발생되는 경우가 있음. 수행 시간이 오래걸리고, 검출 성능이 상대적으로 낮았음. Region Proposal 기법의 등장으로 활용도는 떨어졌지만 Object Detection 발전의 기술적 토대를 제공함. sliding window 방식이 가진 문제점들을 극복하면서 object detection 의 기술들이 고도화됨.



## Region proposal 방식 - Selective Search

*Region proposal*의 대표적인 *Selective Search* 방식을 소개

앞선 *Sliding window* 방식에서는 *detection* 할 *object* 보다 배경을 검색하고 판별하는데 더 많은 시간을 소요하게 된다는 문제점이 존재했습니다. 

```
전체 이미지에서 다 찾지말고 → Object 가 있을 만한 곳을 찾자 → 있을 만한 곳에 Bounding Box 를 추천해 보자 → 추천받은 Bounding Box 에서 최종 후보를 도출 하자
```

이러한 기법이 *Region Proposal* 의 배경 개념

*Selective Search* 가 *Region Proposal* 에서 많이 사용되곤 합니다. 

- 빠른 *Detection* 과 높은 *Recall* 예측 성능을 만족하는 알고리즘
- 색상, 무늬, 크기, 형태, 질감에 따라 유사한 *region* 을 segmentation 해줌. *Selective Search* 는 최초에 개별적으로 조금이라도 만족하는 *pixel* 들을 뽑아 *Over segmentation* 을 수행함. (각각의 *object* 들이 1개의 개별 영역에 담길 수 있도록 많은 초기 *region* 들을 생성)



- 이후 개별 segmentation 들을 유사도가 비슷한 segment 들을 그룹핑해줌.

  

  1. 개별 *segment* 된 모든 부분들을 *bounding box* 로 만들어서 *region proposal* 리스트로 추가
  2. 컬러, 무늬, 크기, 형태에 따라 유사도가 비슷한 segment를 그룹핑함 (*Selective search* 내부의 알고리즘 기준에 의해)
  3. 다시 **[1번 step]** *region proposal* 리스트를 추가, **[2번 step]** 유사도가 비슷한 *segment*들을 그룹핑을 반복하면서 *region proposal* 을 수행함.

- 작은 바운딩 박스부터 시작해서 segmentation 기준에 따라서 점차 합쳐 나감 → bounding box 가 점점 커지면서 어느정도 기준에 다다르면 중요한 object에 대해서 detection 할 수 있도록 bounding box를 비교적 근접하게 추천해줄 수 잇음.



[Selective Search 구현 및 시각화](https://github.com/Dongmin-Sim/Computer_vision/blob/main/ObjectDetection/Selective_Search.ipynb)

*Selective Search* 모듈을 이용하여 *Selective Search* 를 실제 이미지에 적용해보고 bounding box 를 시각화 해봅니다. 

![region_proposal](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/region_proposal.png)



## IOU

Iou : intersection over Union

모델이 예측한 결과와 실측(Groud Truth) Box 가 얼마나 정확하게 겹치는가 ? 를 나타내는 지표



IoU = 개별 박스가 서로 겹피는 영역 / 전체 박스의 합집합 영역
$$
IoU = \frac{Area\:of\:Overlap}{Area\:of\:Union}
$$


예측한 박스가 실측박스와 얼마나 잘 겹쳐지는가

높을 수록 좋음

어느정도 겹쳐야 예측을 했다라고 판단할 수 잇는가? → 그것은 판단에 따라 다름(절대적인 값이 없음)

pascal Voc : IoU 가 0.5 이상이면 예측을 했다고 판단함.

MS coco : 0.5 0.6 0.95 변경시키면서 detection 값을 기준을 높임



## NMS



## mAP





## 참고

[딥러닝 컴퓨터 비전 가이드]()

[딥러닝을 활용한 객체 탐지 알고리즘 이해하기](https://blogs.sas.com/content/saskorea/2018/12/21/%EB%94%A5%EB%9F%AC%EB%8B%9D%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-%EA%B0%9D%EC%B2%B4-%ED%83%90%EC%A7%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0/)

[J.Spin Object Detection 논문 흐름 및 리뷰](https://nuggy875.tistory.com/20?category=860935)

[Object Detection in 20 Years: A Survey](https://arxiv.org/pdf/1905.05055.pdf)

[Resolving Class Imbalance in Object Detection with Weighted Cross Entropy Losses](https://www.semanticscholar.org/paper/Resolving-Class-Imbalance-in-Object-Detection-with-Phan-Yamamoto/b5d0f58ab461342cbbdb53a82f0d7cc190095dde)
