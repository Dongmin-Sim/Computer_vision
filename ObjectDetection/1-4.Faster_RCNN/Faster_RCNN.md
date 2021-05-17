## Fast R-CNN 의 한계

* Selective Seacrch도 좋은 기법이지만, CPU 기반의 느린 수행 속도로 인해 real-time task에 대해 적합한 방법은 아니었음. 
* 완전한 end-to-end 모델이라고 볼 수 없었음. 



## Faster R-CNN 개요

RCNN 계열 모델들 중 가장 뛰어난 성능을 가지고 있음. 기존의 Fast RCNN + RPN 이 결합된 모델이 Faster RCNN 이라고 할 수 있음. 기존에 Selective search 가 하던 역할을 RPN이 대체하여 기존의 CPU 기반의 수행방식을 가지고 있던 것을 GPU 기반의 Deep Learning Network로 변경하였으며 이를 통해 end-to-end 네트워크 구조를 실현하였음. (SVM, selective search와 같은 외부 모듈 x)







