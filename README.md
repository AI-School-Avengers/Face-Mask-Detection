# Face-Mask-Detection
Face Mask Detector using Deep Learning

<br>

## 프로젝트 요약

목표 : 동영상에서 마스크를 쓴사람과 안쓴 사람을 포착하여 영상표기 후 저장하기

- 메인 과제 : 동영상에 데이터에서 마스크를 안쓴사람을 포착해서 표시
- 부가 과제 : ID 부여 후 트래킹을 통해서 마스크 안쓴 사람 추적

<br>

## 데이터 수집 및 전처리

Public Datasets(roboflow, ...), Google crawling

[ffhq 맨얼굴 사진 ](https://github.com/NVlabs/ffhq-dataset).
[마스크 사진](https://github.com/cabani/MaskedFace-Net/blob/master/README.md).
[Kaggle Dataset](https://www.kaggle.com/datasets/andrewmvd/face-mask-detection).
 
#### 마스크 착용 기준
- mask : 코가 보이도록 착용한 것까지(입이나 이가 나오면 안됨)
- no mask : 턱으로 내려서 착용한 것까지(mask 외의 경우)
#### 총 19,482개의 이미지
- mask label : 12307
- no-mask label : 8258

<br>

![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/b1154e04-26a7-4c1a-b4c5-93cb4190dfd8)

<br>

## Create Labels
- Faster R-CNN
- CVAT
  ![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/e15ce071-9d2a-4170-bd53-e45f035d7ff7)
> - 설계한 모델이 annotation 파일이 없으면 test를 수행할 수 없는 구조라 default xml 파일들을 생성하여 testDataset 구축함
> - Kaggle Dataset으로 학습시킨 모델로 test를 진행함
> - test 결과로 나온 얼굴의 bounding box 정보들로 xml 파일을 갱신함
> - YOLO 모델 input에 맞는 format으로 변환함

<br>

## 모델선정
#### 1-Stage Detector VS 2-Stage Detector
상대적인 속도와 정확도 중 컴퓨팅 파워를 고려하여 1-Stage Detector를 채택함

영상 테스트에 강점이 있다고 알려진 Yolo 모델로 선정함

2-Stage Detecotr로 고려했던 Faster R-CNN은 annotation에 활용함

<br>

## 모델 학습
[사용된 YOLO 모델](https://github.com/ultralytics/yolov5/wiki/Train-Custom-Data)

![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/d943963e-b63f-4677-ad8b-8b392be404cf)


팀원들과 분담하여 S/L/X 모형으로 학습한 결과, 모델 복잡도가 높아질수록 정확도가 높아짐

하지만 프로젝트 시간과 컴퓨터 환경을 고려하여 5s 모형 선택하게 됨

<br>

## Train
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/8cd62191-a536-4440-80fa-e1480db4e81b)

#### 사용한 Arguments
**--weights** : initial weights path

**--cfg** : model.yaml path

**--data** : dataset.yaml path

**--epochs** default=300

**--batch-size** default=16

**--img-size** default=640

**--resume** : resume most recent training

<br>

## 초기 2914개의 이미지로 학습한 모델 테스트
mask : no-mask = 4 : 1
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/71ac493c-b90e-4ffa-961a-8aeecb3ad49d)
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/41090910-ef3d-40e0-a8fc-c36e699e4102)
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/9e27f076-6915-442d-9619-7fe2eae0b6fd)

![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/8c2e4698-d6b9-44ae-9ef8-c76079d3cf36)

#### 테스트 결과
> 마스크를 착용한 것은 얼추 잡는데에 반해 마스크를 미착용을 잘 잡지 못함
> 추가 데이터 수집이 필요함 (특히 **no-mask!** )

<br>

## 최종 모델 테스트
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/2f29476e-7300-4adf-8656-4c282a2762df)
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/be4d4fdc-20be-4578-bf03-aec2c723b87a)
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/43a65a85-99df-4fad-8692-871d9322bcb9)
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/30dc9661-d65f-43d2-a81f-f9d7ffdf54b0)

<br>

## 트래킹 및 결과 시각화
[트래킹에 사용한 모델](https://github.com/mikel-brostrom/yolo_tracking).

성능 : mAP@IoU=0.75

### DeepSort
사용한 Arguments

**--yolo_weights** : model.pt path

**--source** : video source path

**--iou-thres** : IOU threshold 0.75

**--save-vid** : save video tracking results

**--classes** : filter by class

**--blurid** : fillter by id, blur

<br>


![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/b724e55d-9c94-49bf-b000-3d2b8fd5bef1)
> no-mask 빨간색 표시. --blurid로 원하는 id blur처리(ex 1)

<br>

## 부족한 점
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/fa78fdfb-9d06-4387-a4f2-6c1e8741853e)
![image](https://github.com/AI-School-Avengers/Face-Mask-Detection/assets/37794363/3ad4f814-df46-4123-9d30-086577b16ebe)
- 화면 전환 시, 동일 위치 동일 대상으로 인식하는 경향이 있음

- 처음에 no-mask로 detect하면 mask를 착용해도 no-mask로 지속되는 현상 존재
 
- 정면인 경우에도 틀린 라벨링 존재
 
- 데이터 보충과정에서 여러 각도의 이미지를 넣지 못함
 
- 영상을 저장해서 살펴보는 방식 외에 실시간으로도 트래킹해봤으면 좋았을 거 같다. 
