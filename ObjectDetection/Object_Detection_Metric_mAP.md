## Object Detecion 성능 평가 Metric - mAP

실제 object가 Detected 된 재현율(Recall) 의 변화에 따른 정밀도(Precision)의 값을 평균한 성능 수치

![mAP](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Metric_mAP/mAP)

[참고 : 분류성능평가지표 - Precision(정밀도), Recall(재현율) and Accuracy(정확도)](https://sumniya.tistory.com/26)

### 정밀도, 재현율

정밀도(Precision)와 재현율(Recall)은 주로 이진분류(Binary Classificaion)에서 사용되는 성능지표임.

- 정밀도 (Precision)
  - 예측을 Positive 로 한 대상 중에 예측값과 실제값이 Positive 로 일치한 데이터의 비율을 뜻함. Object Detection 에서는 검출 알고리즘이 검출을 **예측한 결과**가 **실제 Object 와 얼마나 일치**하는지를 나타내는 지표
  - 검출 예측한 결과가 실제와 일치하는가 ?
- 재현율(Recall)
  - 실제 값이 Positive 인 대상 중에 예측과 실제 값이 Positive 로 일치한 데이터의 비율을 뜻함. Object Detection 에서는 검출 알고리즘이 **실제 Object 들을 빠뜨리지 않고 얼마나 정확히 검출** 예측하는 지를 나타내는 지표
  - 전체 object 중에 얼마나 많이 정확하게 검출을 했는가?

좋은 알고리즘이라면 정밀도와 재현율이 모두 좋아야 함. 한 지표만 극단적으로 높거나 낮은 알고리즘은 좋은 알고리즘이 아님. 두 지표가 균형있게 높아져야 좋은 알고리즘이라고 평가할 수 있음.

IOU 값에 따라 Detection 예측 성공 결정

Object Detection 에서 개별 Object에 대한 검출()예측이 성공하였는지의 여부를 IOU로 결정

일반적으로 PASCAL VOC challenge 에서 사용된 기준을 적용하여 IOU가 0.5 이상이면 예측 성공으로 인정.

(그러나 COCO challenge 에서는 여러개의 IOU 기준을 변경해가면서 예측성공을 적용)

### 오차행렬(confusion matrix)

![ConfusionMatrix](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Metric_mAP/ConfusionMatrix.png)

Confusion matrix

오차행렬은 이진 분류의 예측 오류가 얼마인지와 더불어 어떠한 유형의 예측 오류가 발생하고 있는지 함께 나타내는 지표입니다.

### Object Detection 에서 TP, FP, FN 에 따른 정밀도와 재현율

- **TP**

![TP](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Metric_mAP/FP.png)

예측한 값이 실제값과 정확히 일치한 경우를 뜻합니다. 정밀도와 재현율의 분자와 분모의 값으로 들어가게되며 TP 의 반대인 TN 은 사용되지 않습니다. 왜냐하면 일반적으로 Positive 에 대한 정보를 알고 싶은 task들이 많기 때문에 예측한 값이 실제값과 모두 일치하지 않는 경우인 TN 은 지표에서 사용되지 않습니다.

------

![FP](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Metric_mAP/FP.png)

- **정밀도**

$$Precision = \frac{TP}{(FP+TP)}$$

정밀도는 앞에서 확인했듯이 False positive, Negative 를 Positive 로 잘못 예측한 경우를 말함. Object Detection 에서는 클래스 분류가 잘못된 경우, IOU 값이 기준치에 비해 작아 겹치는 부분이 적은 경우, 아예 겹치는 부분이 없거나 다른 Object 를 검출했을 경우들이 이에 FP에 해당 됩니다.

따라서 정밀도를 높이려면 이러한 FP(잘못된 positive) 값이 낮아야 함.

------

![FN](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Metric_mAP/FN.png)

- **재현율**

$$
Recall = \frac{TP}{(FN+TP)}
$$

재현율을 실제 존재하는 Object 들을 얼마나 많이 검출에 성공했느냐 에 대한 지표입니다. FN 의 경우가 재현율 지표를 좌우하는데 False Negative, Positive 를 Negative 로 잘못(False) 예측한 경우를 말합니다. Object Detection 에서는 검출 자체를 못한 경우를 의미합니다.

따라서 재현율을 높여주려면 이러한 FN(잘못된 Negative) 값이 낮아야 함.

정밀도와 재현율은 모두 중요한 지표이나, 해결하고자 하는 문제에 따라 상대적으로 그 중요도가 달라집니다. 예를들어 **정밀도**가 상대적으로 더 중요한 경우들은 (Negative 인 데이터를 Positive 로 잘못 예측하는 경우, predict가 정확해야 하는 업무) 업무관련 메일을 스팸으로 처리하는 경우가 있을 수 있고,

**재현율**이 상대적으로 더 중요한 경우들은(Positive인 데이터를 Negative 로 잘못 예측하는 경우, detection을 놓쳐서 안되는 경우) 암진단과 같은 경우가 있을 수 있습니다. 대체적으로는 재현율이 더 중요한 업무들이 많으나, 일부 정밀도가 중요한 업무들도 존재함.

### 정밀도와 재현율 주의사항

두 지표는 서로 보완적인 Trade-Off 관계에 있는 지표이기 때문에 두 지표 모두 고르게 고려가 되어야 함. 간혹 두 지표중 하나의 지표에 극단적으로 치우치는 경우가 생길 수 있는데 이는 다음과 같습니다.

- 정밀도가 100% 가 되는 경우

  Positive 가 확실한 경우만 Positive 로 예측하고 조금이라도 Negative 일 확률이 있는 후보군들, 나머지를 모두 Negative 로 예측하면, 예측한 값은 모두 실제값과 일치하게 되므로 FP=0 가 됩니다.

  Object Detection 의 경우에는 확실한 경우만 Bounding box를 그리고 나머지에 대해서는 검출하지 않게 됩니다.

- 재현율이 100% 가 되는 경우

  모든 경우를 Positive 로 예측하면 됩니다. $\frac{TP}{(FN + TP)}$ 이므로 사실상 검출하지 못한 값은 없기 때문에 FN 은 0 이 되어 재현율이 100% 가 되게 됩니다. negative 인값을 negative 로 예측한 비율인 TN은 지표에 사용되지 않기 때문에 이러한 현상이 발생하게 됩니다.

  Object Detection 의 경우에는 조금이라도 가능성이 있는 모든 후보군에 대해서 Bounding box 를 그리게 됩니다.

### Confidence Threshold 에 따른 정밀도와 재현율 변화

후보 임계값이라고 볼 수 있는 Confidence Threshold 이 낮아지면 그만큼 Bounding box 후보군이 많아지게 되므로 재현율의 값은 높아지게 되고, 반대로 Confidence Threshold 이 높아지면 Boundinf box 후보를 예측하는 것이 엄격해지는 것이기 때문에 그만큼 Bounding box 후보군이 적어지게 되고 정밀도의 값은 높아짐

![ConfidenceThreshold](https://github.com/Dongmin-Sim/Computer_vision/blob/main/keynote/img/Metric_mAP/ConfidenceThreshold.png)

이러한 특징 때문에 정밀도와 재현율은 서로 보완적인 평가 지표가 되게됩니다. 어느 한쪽이 높아지면 다른 한쪽이 낮아지는 관계가 성립이 되는데 이를 정밀도/재현율의 Trade-off 라고 부릅니다.

위의 관계를 그래프로 나타낸 것이 Precision-Recall Curve 입니다. Recall 값의 변화에 따른 Precision 값을 나타낸 그래프, 곡선을 정밀도-재현율 곡선이라고함. 안쪽의 면적구한 값을 Precision 값의 평균=AP 라고 합니다.
