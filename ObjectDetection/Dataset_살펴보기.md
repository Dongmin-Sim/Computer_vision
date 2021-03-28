# 주요 Dataset

많은 Detection 과 segmentation 분야에서 주로 사용되는 데이터 세트들은 다음과 같음. 

* PASCAL VOC
* MS COCO
* Google Open Images

`Image classificaion` 에서는 주로 **CIFAR-10(100)**, **MNIST**, **ImageNet** 등이 존재하는 것처럼 이미지 `detection` 과 `segmentation` 에서는 위와 같은 데이터 세트들이 가장 유명하고 논문에도 자주 사용됨. 



## PASCAL VOC

PASCAL VOC challenge 는 컴퓨터 비전에서 object recognition 관련된 task를 다루는 대회임. 총 20개 카테고리를 가지고 있고, 영상에서 특정 object 를 검출해내는 성능을 겨루는 대회임. 많은 유명한 object detection 기술들이 이 대회를 통해 발표가 되기도 했으며 논문에서도 주로 쓰이는 데이터 세트임. segmentation 대회도 같이 진행했었음. 



* PASCAL VOC 는 이미지에 대한 `annotation` 값이  `xml` 로  이루어져 있으며 각 이미지에 존재하는 object의 클래스와 바운딩 박스를 나타내는 좌표가 입력되어 있음. 
* PASCAL VOC 에서 성능지표로 IOU 값을 사용했음. 



[PASCAL VOC 공식 홈페이지](http://host.robots.ox.ac.uk/pascal/VOC/) , 접속이 안되는 경우가 있을때는 [pascal voc mirror](https://pjreddie.com/projects/pascal-voc-dataset-mirror/) 사이트에서 데이터세트 다운로드 가능함. 



### 데이터 구조







### 원본 이미지



### Annotation





```python

```





## MS COCO

MS COCO 는 PASCAL VOC 데이터 세트 이후에 나오게 되었으며 object detection, segmentation 에 있어서 실제에 가까운 데이터가 필요하게 됨으로써 이를 잘 대변해주는 데이터 세트라고 할 수 있음. 한 영상에 여러개의 클래스들이 있고, contest 의 경우 PASCAL VOC 에 비해 엄격한 기준을 적용. 많은 딥러닝 패키지들이 MS COCO 로 pre_trained 되어 배포되기도 함. 



* 이미지들에 대한 설명은 json 포맷으로 이루어져 있으며 80여개의 object 카테고리를 가지고 있음. **330K images** 의 이미지와 1.5만개의 instances 를 가지고 있다고 함.

* 구글이 공개한 TensorFlow Object Detection API에 coco 데이터 세트로 pre_train 시킨 모델도 존재함. 



[MS COCO 공식 홈페이지](https://cocodataset.org/#home)



## Google Open Images

구글에서 2018년도에 내놓은 object classificaion, detection, segmentation .. 등을 위한 이미지 데이터 세트. 현재(2021. 3기준)는 version 6 까지 나왔으며 새로운 시각적인 관계(손을 잡고 있는 사람들), 행동주석(달리기, 점프), 등등 다양한 주석을 제공하고 있음. 



* annotation 포맷을 `csv` 로 되어 있으며 다음과 같은 특징을 가지고 있음.

  15,851,536 boxes on 600 categories

  2,785,498 instance segmentations on 350 categories

  3,284,282 relationship annotations on 1,466 relationships

  675,155 localized narratives

  59,919,574 image-level labels on 19,957 categories

  Extension - 478,000 crowdsourced images with 6,000+ categories



open images 데이터 세트에 대한 더 자세한 사항은 [Google open images v6](https://storage.googleapis.com/openimages/web/factsfigures.html)  부분을 참고! 

[Google AI Blog v6의 새로운 기능(Narrratives) 에 대한 설명](https://ai.googleblog.com/2020/02/open-images-v6-now-featuring-localized.html)



![open images v6](https://1.bp.blogspot.com/-rUGHTEFowDY/XlbQgaYW5II/AAAAAAAAFYQ/NJOxWGEs9HcGeZOOuMhdZoRrPqFcH79-QCEwYBhgL/s640/image2.png)



## PETS

**Performance Evaluation of Tracking and Surveillance**





---

### 참고

* 딥러닝 컴퓨터비전 완벽가이드 - 권철민

* https://bskyvision.com/491

* https://medium.com/@arthur_ouaknine/review-of-deep-learning-algorithms-for-image-semantic-segmentation-509a600f7b57
* https://89douner.tistory.com/113
* https://ndb796.tistory.com/500



### 이미지 출처

* https://ai.googleblog.com/2020/02/open-images-v6-now-featuring-localized.html





