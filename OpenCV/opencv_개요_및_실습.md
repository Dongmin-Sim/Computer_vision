# Python 기반 주요 이미지 라이브러리 소개



## PIL(Python Image Library)

* 주로 이미지 처리만을 위해 사용
* 처리속도가 상대적으로 느리다는 특징이 있음.



## Scikit Image

* 파이썬 기반의 컴퓨터 비전 기능을 제공함. 

* Scipy, numpy 기반으로 이루어져 있음



## OpenCV

* 오픈소스 기반의 최고의 컴퓨터 비전 라이브러리
* 컴퓨터 비전 기능을 일반화 하는데 큰 기여를 한 라이브러리



초기 인텔이 개발을 주도하였고, 다양한 OS에서 사용이 가능함. **방대한 컴퓨터 비전 관련 라이브러리**를 제공한다는 특징이 있음. 





# OpenCV 이미지 로딩

[실습 소스코드 보러가기](https://github.com/Dongmin-Sim/Computer_vision/blob/main/OpenCV/OpenCV.ipynb)

## imread()

OpenCV 에서 이미지를 불러오고자 할때는 `imread('filename')` 을 사용해서 불러올 수 있음. `mired('filename')` 은 이미지 파일을 numpy array 로 변환해서 불러오는 방식을 가지는데 OpenCV에서 `imread()`를 사용할 경우에 한가지 주의 사항이 존재함. 

OpenCV 가 불러오는 이미지는 우리가 흔히 사용하는 **RGB 가 아닌 BRG 형태로 가져오기 때문에** 원본이미지의 색과는 차이가 존재함. 

```python
import cv2
import matplotlib.pyplot as plt

# imread 로 이미지 불러오기
bgr_img = cv2.imread('../data/image/red_wall.jpg')
plt.figure(figsize=(10, 10))
plt.imshow(img)
```

![imread](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/OpenCV_%EA%B0%9C%EC%9A%94_%EB%B0%8F_%EC%8B%A4%EC%8A%B5/imread.png?raw=true)

## cvtColor()

BGR 형태를 RGB 로 바꾸어 주기 위해서는 `cvtColor(image, cv.COLOR_BGR2RGB)` 를 사용하여 변환할 수 있음. 

```python
import cv2
import matplotlib.pyplot as plt

bgr_img = cv2.imread('../data/image/red_wall.jpg')
rgb_img = cv2.cvtColor(bgr_img, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(10, 10))
plt.imshow(rgb_img)
```

![cvtColor](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/OpenCV_%EA%B0%9C%EC%9A%94_%EB%B0%8F_%EC%8B%A4%EC%8A%B5/cvtColor.png?raw=true)

## imwrite()

OpenCV에서 이미지를 다시 저장하려면 `imwrite('filename', image)` 를 아용하면 됨.
`imwrite()` 는 BGR 형태의 이미지를 RGB 형태로 변환하여 저장한다는 특징이 있음.

실제로 저장된 파일을 보면 원본이미지와 동일한 색상을 가지는 것을 확인할 수 있음 



## OpenCV windows Frame

OpenCV 는 OS 의 window Frame 과 바로 인터페이스 하여 사용할 수 있는 여러 기능들을 제공. Window Frame 생성이 가능한 GUI 개발 환경에서만 가능함. 따라서 주피터 노트북 기반에서는 오류가 발생함. 



* `cv2.imshow()` : 이미지를 window frame 에 보여줌.
* `cv2.waitKey() `: 키보드 입력이 있을때 까지 무한 대기
* `cv2.destroyAllWindows()` : 화면의 윈도우 프레임 모두 종료





# OpenCV 활용 영상 처리

[실습 소스코드 보러가기](https://github.com/Dongmin-Sim/Computer_vision/blob/main/OpenCV/OpenCV.ipynb)

## VideoCapture

`cv2.VideoCapture` : 동영상을 개별 Frame 으로 하나씩 읽어오는 기능

`cv2.Videowriter` : VideoCapture 로 읽어들인 Frame 을 동영상으로 다시 write 하는 기능



VideoCapture 를 사용하는 방법은 VideoCapture 객체 생성자를 만들어서 주로 사용함

```python
cap = cv2.VideoCapture('input_video_path', index)
```

- input_video_path : 동영상 경로
- index : 카메라 장치 번호, 0번부터 시작
- cap : VideoCapture 객체



### get, set 함수

이러한 `VideoCapture` 객체는 영상, 카메라의 속성을 **확인**하고 **설정**할 수 있는 **get, set** 함수를 제공함
`get(id)`에 **속성 아이디**를 전달하면 해당 **입력 동영상 속성의 값**을 알 수 있음
`set(id, value)`에 **속성 아이디**와 **값**을 전달하면 **입력 동영상 속성을 지정**할 수 있음.

```python
# 입력 동영상의 frame 너비 확인
cap.get(cv2.CAP_PROP_FRAME_WIDTH)

# 입력 동영상의 frame 너비(320) 설정
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 320)
```

주요한 'cv2.CAP*PROP*' 속성 ID

- `cv2.CAP_PROP_FRAME_WIDTH` : 프래임 폭, 너비
- `cv2.CAP_PROP_FRAME_HEIGHT` : 프래임 높이
- `cv2.CAP_PROP_FPS` : 초당 프레임 수



### Read()함수

`VideoCapture` 객체는 `read()` 함수를 통해 동영상의 영상의 프레임을 읽을 수 있음
`read()` 함수는 프레임이 정상적으로 읽어졌는지 확인할 수 있는 **Boolean** 과 한개의 프레임 이미지를 표현하는 **Numpy 배열 객체**를 쌍으로 갖는 **튜플을 반환**함.

```python
while True:
    # cap 객체의 프레임을 read() 함수를 통해 차례대로 읽어옴
    # frame 이 정상적으로 읽어지면 read_frame에 True, img_frame 에는 이미지 배열이 들어오게 됨.
    read_frame, img_frame = cap.read()
    # if 조건절을 통해 동영상의 마지막 frame이 끝났을때 반복문을 나올 수 있게
    if not read_frame:
        print('동영상의 frame이 끝났습니다')
        break
```



## VideoWrite

`VideoWrite` 를 사용하는 방법은 `VideoCapture` 와 마찬가지로 `VideoWrite` 객체생성자를 사용하며

```python
video_writer = cv2.VideoWriter(video_path, fourcc, fps, (width, height))
```

- video_path : 저장할 비디오 경로
- fourcc : Encoding 코덱 유형(4글자), write 시 특정 포맷으로 동영상을 Encoding 가능(DIVX, XVID, MJPG, X264, WMV1, WMV2)
- fps : 초당 프레임수
- (width, height) : 비디오의 너비와 높이(튜플 형식)

위와 같은 인잣값들을 입력받음

`cv2.VideoWriter_fourcc()`함수 : 4개의 인코딩 포맷문자를 전달하면 코드 값을 생성해내는 함수
해당 코덱이 있는지 잘 확인하고, encoding 시 설정하는 코덱와 저장하는 동영상의 확장자명을 확인해서 잘 설정해주어야 함.

다음과 같은 형식으로 많이 사용됨.

```python
cap = cv2.VideoCapture(input_video_path)

codec = cv2.VideoWriter_fourcc(*'DIVX')

video_width = round(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
video_height = round(cap.get(cv2.CAP_PROP_FRAM_HEIGHT))

video_fps = cap.get(cv2.CAP_PROP_FPS)

video_writer = cv2.VideoWriter(video_path, codec, video_fps, (video_width, video_height))
```



## 영상 실습

아래의 영상을 `VideoCapture` 로 영상을 불러와 `get()` 함수를 통해 영상의 frame 개수, fps, frame 폭과 높이 확인이 가능함. 

```python
frame 개수 : 125, FPS : 25.0, Frame 크기 : (1280, 720)
```

![videoCapture](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/OpenCV_%EA%B0%9C%EC%9A%94_%EB%B0%8F_%EC%8B%A4%EC%8A%B5/videoCapture.png)



위의 영상을`VideoCapture` 객체의 `read()` 함수를 통해 Frame 마다 읽어와 rectangle 및 Frame 번호를 Text를 그리고 다시 `VideoWriter` 을 통해 영상을 저장한 결과물이 아래와 같음.

![videoWriter](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/OpenCV_%EA%B0%9C%EC%9A%94_%EB%B0%8F_%EC%8B%A4%EC%8A%B5/videoWriter.png)

