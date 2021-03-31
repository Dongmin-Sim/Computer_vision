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

### imread()

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

### cvtColor()

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

### imwrite()

OpenCV에서 이미지를 다시 저장하려면 `imwrite('filename', image)` 를 아용하면 됨.
`imwrite()` 는 BGR 형태의 이미지를 RGB 형태로 변환하여 저장한다는 특징이 있음.

실제로 저장된 파일을 보면 원본이미지와 동일한 색상을 가지는 것을 확인할 수 있음 



### OpenCV windows Frame

OpenCV 는 OS 의 window Frame 과 바로 인터페이스 하여 사용할 수 있는 여러 기능들을 제공. Window Frame 생성이 가능한 GUI 개발 환경에서만 가능함. 따라서 주피터 노트북 기반에서는 오류가 발생함. 



* cv2.imshow() : 이미지를 window frame 에 보여줌.
* cv2.waitKey() : 키보드 입력이 있을때 까지 무한 대기
* cv2.destroyAllWindows() : 화면의 윈도우 프레임 모두 종료



# OpenCV 활용 이미지 처리 

