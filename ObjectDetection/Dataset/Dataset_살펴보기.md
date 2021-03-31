# 주요 Dataset

많은 Detection 과 segmentation 분야에서 주로 사용되는 데이터 세트들은 다음과 같음. 

* [PASCAL VOC](#pascal-voc)
* [MS COCO](#ms-coco)
* [Google Open Images](##google-open-images)

`Image classificaion` 에서는 주로 **CIFAR-10(100)**, **MNIST**, **ImageNet** 등이 존재하는 것처럼 이미지 `detection` 과 `segmentation` 에서는 위와 같은 데이터 세트들이 가장 유명하고 논문에도 자주 사용됨. 



## 1. PASCAL VOC
[소스코드 확인](https://github.com/Dongmin-Sim/Computer_vision/blob/main/ObjectDetection/Dataset/PascalVOC_dataset.ipynb)

PASCAL VOC challenge 는 컴퓨터 비전에서 object recognition 관련된 task를 다루는 대회임. 총 20개 카테고리를 가지고 있고, 영상에서 특정 object 를 검출해내는 성능을 겨루는 대회임. 많은 유명한 object detection 기술들이 이 대회를 통해 발표가 되기도 했으며 논문에서도 주로 쓰이는 데이터 세트임. segmentation 대회도 같이 진행했었음. 



* PASCAL VOC 는 이미지에 대한 `annotation` 값이  `xml` 로  이루어져 있으며 각 이미지에 존재하는 object의 클래스와 바운딩 박스를 나타내는 좌표가 입력되어 있음. 
* PASCAL VOC 에서 성능지표로 IOU 값을 사용했음. 



[PASCAL VOC 공식 홈페이지](http://host.robots.ox.ac.uk/pascal/VOC/) , 접속이 안되는 경우가 있을때는 [pascal voc mirror](https://pjreddie.com/projects/pascal-voc-dataset-mirror/) 사이트에서 데이터세트 다운로드 가능함. 



### 1.1 PASCAL VOC 살펴보기

#### 1.1.1 데이터세트 파일 구조

![image-20210328201541937](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Dataset_%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/image-20210328201541937.png?raw=true)

* `Annotations` : 원본 이미지에 대응되는 xml 파일들이 위치하고 있음. Image 에 대한 annotation 정보가 `xml`에 담겨 있음
* `JPEGImages` : 원본 이미지가 위치하고 있음
* `SegmentationObject` : Semantic Segmentation 을 위한 masking 이미지
* `SegmentationClass`: Instance Segmentation 을 위한 masking 이미지
* `ImageSets `: train, val, trainval, test 에 사용할 이미지의 정보를 object 별로 txt파일로 위치하고 있음. 



#### 1.1.2 JPEGImages (original image)

`2007_000033` 의 원본 이미지

```python
import cv2
import matplotlib.pyplot as plt
%matplotlib inline

img = cv2.imread('VOCdevkit/VOC2012/JPEGImages/2007_000033.jpg')
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
print('img shape : ', img.shape)

plt.figure(figsize=(10, 10))
plt.imshow(img_rgb)
plt.show()
```

![image-20210328202907978](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Dataset_%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/image-20210328202907978.png?raw=true)



#### 1.1.3 Annotation

`2007_000033` 의 xml 파일

```python
!cat VOCdevkit/VOC2012/Annotations/2007_000033.xml
```

![image-20210328202840638](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Dataset_%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/image-20210328202840638.png?raw=true)



#### 1.1.4 SegmentationObject (masking image)

`2007_000033` 의 masking image

```python
img = cv2.imread('VOCdevkit/VOC2012/SegmentationObject/2007_000033.png')
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
print('img shape : ', img.shape)

plt.figure(figsize=(10, 10))
plt.imshow(img_rgb)
plt.show()
```

![image-20210328203624579](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Dataset_%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/image-20210328203624579.png?raw=true)



#### 1.1.5 Annotation 을 이용하여 object 의 정보 parsing 

xml 파싱을 위한 파일불러오기 및 루트노드 확인. 링크 주소 참고
```python
import xml.etree.ElementTree as et
tree = et.parse(xml_dir)
root = tree.getroot()
```
[XML 파싱하는 방법, Hello World! 파이썬 3(WikiDocs)](https://wikidocs.net/21140)

```python
import os
import xml.etree.ElementTree as et

xml_dir = 'VOCdevkit/VOC2012/Annotations/2007_000033.xml'

# xml 파싱을 위한 tree 객체 생성 
tree = et.parse(xml_dir)
# root 노드 가져오기
root = tree.getroot()

# image 이름
image_name = root.find('filename').text
print('image name : ', image_name)

# 이미지 사이즈
img_size = root.find('size')
img_width = int(img_size.find('width').text)
img_height = int(img_size.find('height').text)
print('img_width :', img_width)
print('img_height :', img_height)

objects = []
# xml 파일 내에 있는 object 를 모두 찾음
for obj in root.findall('object'):
    name = obj.find('name').text
    
    # object의 자식 element 에서 bndbox 를 찾음 
    bndbox = obj.find('bndbox')
    # bndbox 의 자식 element 에서 xmin, tmin, xmax, ymax 를 찾음
    x1 = int(bndbox.find('xmin').text)
    y1 = int(bndbox.find('ymin').text)
    x2 = int(bndbox.find('xmax').text)
    y2 = int(bndbox.find('ymax').text)
    
    bndbox_cordinate = (x1, y1, x2, y2)
    
    # 사전 형식으로 object name 과 좌표 저장
    obj_dic = {
        'name': name,
        'bndbox_cord': bndbox_cordinate
    }
    # 리스트에 추가
    objects.append(obj_dic)
    
for obj in objects:
    print(obj)
```

```python
out[]:
image name :  2007_000033.jpg
img_width : 500
img_height : 366
{'name': 'aeroplane', 'bndbox_cord': (9, 107, 499, 263)}
{'name': 'aeroplane', 'bndbox_cord': (421, 200, 482, 226)}
{'name': 'aeroplane', 'bndbox_cord': (325, 188, 411, 223)}
```



#### 1.1.6 Annotation xml 을 통해 bounding box 시각화

```python
import cv2
import xml.etree.ElementTree as et

xml_dir = 'VOCdevkit/VOC2012/Annotations/2007_000033.xml'

tree = et.parse(xml_dir)
root = tree.getroot()

# img_name parsing
img_name = root.find('filename').text
print('img_name : ', img_name)

img_dir = 'VOCdevkit/VOC2012/JPEGImages/'
img = cv2.imread(img_dir+img_name)

# cv2 는 rectangle인자로 받은 이미지배열에 사각형을 그려주므로 새로운 이미지에 작업 수행
img_bnd = img.copy()

# cv2 는 BGR 이므로 RGB 순서와 반대로임
red_color = (0, 0, 255)
green_color = (0, 255, 0)

for obj in root.findall('object'):
    obj_name = obj.find('name').text
    
    obj_bndbox = obj.find('bndbox')
    left = int(obj_bndbox.find('xmin').text)
    top = int(obj_bndbox.find('ymin').text)
    right = int(obj_bndbox.find('xmax').text)
    bottom = int(obj_bndbox.find('ymax').text)
    
    # xml 에서 추출한 bounding box 의 left, top, right, bottom 좌표값을 이용하여 bounding box 시각화
    cv2.rectangle(img_bnd, (left, top), (right, bottom), color=green_color, thickness=2)
    # xml 에서 추출한 obj의 name 을 이용하여 bounding box 위에 표시
    cv2.putText(img_bnd, obj_name, (left, top-5), fontFace=cv2.FONT_HERSHEY_COMPLEX, fontScale=0.4, color=red_color, thickness=1)

# BGR -> RGB 변환
img_bnd = cv2.cvtColor(img_bnd, cv2.COLOR_BGR2RGB)
plt.figure(figsize=(10, 10))
plt.imshow(img_bnd)
plt.show()
```

![image-20210328220956036](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Dataset_%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/image-20210328220956036.png?raw=true)

## 2. MS COCO

MS COCO 는 PASCAL VOC 데이터 세트 이후에 나오게 되었으며 object detection, segmentation 에 있어서 실제에 가까운 데이터가 필요하게 됨으로써 이를 잘 대변해주는 데이터 세트라고 할 수 있음. 한 영상에 여러개의 클래스들이 있고, contest 의 경우 PASCAL VOC 에 비해 엄격한 기준을 적용. 많은 딥러닝 패키지들이 MS COCO 로 pre_trained 되어 배포되기도 함. 



* 이미지들에 대한 설명은 json 포맷으로 이루어져 있으며 80여개의 object 카테고리를 가지고 있음. **330K images** 의 이미지와 1.5만개의 instances 를 가지고 있다고 함.

* 구글이 공개한 TensorFlow Object Detection API에 coco 데이터 세트로 pre_train 시킨 모델도 존재함. 



[MS COCO 공식 홈페이지](https://cocodataset.org/#home)



## 3. Google Open Images

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



## 4. PETS

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





