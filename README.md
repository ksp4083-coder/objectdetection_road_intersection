# YOLO : 신호등 교차로 객체 탐지
- [제2회 경기도 자율주행센터 자율주행 데이터 활용 경진대회](https://ggzerocity.or.kr/?p=38&page=1&viewMode=view&reqIdx=202408291933131878)에 팀으로 참가해 진행한 프로젝트입니다.
- YOLO 모델의 고질적인 문제인 소형 객체 탐지 성능을 높이는 방법을 고안했으며, 신호등 교차로 이미지를 영상으로 변환해 객체 탐지를 수행합니다.
- 모델 개발 아이디어에서 높은 점수를 받아 [우수상](https://graceful-cello-0d4.notion.site/2-2397d8d98aa880aa84c1d23e3a2132d8?source=copy_link)을 수상했습니다.

<Br>

## 배경
- 대회 기간 : <ins>2024. 10. 11. ~ 2024. 12. 13.</ins>
- 학교 팀 프로젝트 과목에서 좋은 점수를 받기 위해 참여함
- 프로젝트를 진행하면서 팀원 간에 소통의 중요성을 깨닳고 공모전 수상을 위한 아이디어를 얻었으며 YOLO 모델의 단점을 보완해볼 수 있었음

<Br>

## 사용 데이터
- 주최측에서 제공된 ['공간데이터마켓 자율주행 도로인프라 데이터'](https://drive.google.com/drive/folders/1M-h3gC3zlh-ouqTvU_44FY_p22i7qXXI?usp=sharing)(4.0GB)
  - GitHub의 코드는 [구글 드라이브](https://drive.google.com/file/d/1V_gli7OmBji3p9X1z23ipHjH_9-fqxw6/view?usp=sharing)에 저장된 데이터를 사용함
  - 이미지는 동일하지만 라벨 표기가 옳바르게 되어 있어서 이미지를 빠르게 넘기면 실시간으로 객체가 움지이는 것을 확인할 수 있음

- 1920x1200 크기 신호등 교차로 이미지 1만 장과 8,000 장의 학습 이미지에 대한 객체 8종의 위치 정보 파일(train.json) 1개가 제공됨
  - 학습 데이터셋 : 7,000 images 
  - 테스트 데이터셋 : 3,000 images

- 7,000 장의 이미지를 8:2의 비율로 학습과 검증 데이터셋으로 나눔
  - 일반적으로 머신러닝에서 원본 데이터를 학습과 검증 데이터셋으로 나눌 때 7:3의 비율로 나눔
  - [이전 공모전](https://github.com/ksp4083-coder/objectdetection_driving2.git)에서 80,000 장의 이미지로 객체 탐지 모델을 개발했을 때 일주일간 모델을 학습시켰음에도 정확도(mAP, IoU threshold = 0.5) 0.93에 그쳤음
  - 이번 공모전의 경우 정답지(객체 위치 정보)가 제공된 이미지는 7,000 장이며 정확도를 높이기 위해 7,000 장의 이미지를 8:2의 비율로 학습과 검증 데이터셋으로 나눔    
  - 3,000 장의 이미지는 정답지가 제공되지 않아 모델 학습에 활용할 수 없었음
- 객체 위치 정보 파일(.json) 속성

  <img width="500" height="212" alt="image" src="https://github.com/user-attachments/assets/e8678848-f806-4689-a236-fb5a6ad662ff" />

<br>

## 데이터 전처리
### 1. 이미지 & 객체 위치 정보 추출
- YOLO 모델을 학습하기 위해 train.json 파일에서 이미지 7,000 장에 대한 이미지 정보와 객체 위치 정보를 추출하고,
- <ins>*data_road_intersection/labels_json*</ins> 폴더에 각각에 이미지에 대한 json 파일 형식으로 저장함
- 이미지 별 객체 위치 정보 파일(.json) 예시 <br>
  
  <p align="center">
  <img width="597" height="325" alt="image" src="https://github.com/user-attachments/assets/06e7ba24-aa2c-4411-b1e4-2a146c82ea54" />
  </p><br>

  ### 1-1. 정답지가 제공된 객체 수 확인
  - 정답지는 객체 위치 정보를 뜻하며 모델 학습과 검증에 활용할 수 있는 객체 수를 확인함
  - <ins>*data_road_intersection/labels_json*</ins> 폴더 내 모든 json 파일을 하나씩 읽어서 annotations 변수 안에 있는 lbl_nm(객체 이름)을 추출하고 객체 이름을 기준으로 개수를 셈 <br>

    | class | count |                                      
    |:-----|:-----:|
    | car | 77,368 |
    | sign | 21001 |
    | human | 9734 |
    | truck | 5159 |
    | bus | 4662 |
    | taxi | 2651 |
    | motorcycle | 1609 |
    | special_vehicles | 967 |

<br>

### 2. 객체 위치 정보 정규화
- 객체 위치 정보 정규화는 YOLO 객체 탐지 모델을 학습시키기 위해 YOLO 형식 객체 위치 정보 파일을 생성하는 과정임 
- YOLO 객체 탐지 모델은 직사각형으로 탐지한 객체의 위치를 나타냄
- 해당 직사각형을 Bounding Box, 약자로 BBox라고 함
- 모델이 객체의 특성을 학습하고 새로운 이미지에서 객체 탐지를 수행하기 위해 객체 위치 정보를 모델에게 알려주어야 하는데
- YOLO의 경우 YOLO 형식 객체 위치 정보가 포함된 파일의 경로를 yaml 파일에 지정해서 모델이 객체의 특성을 학습할 수 있도록 함 <br><br>

  #### **2-1. YOLO 형식 객체 위치 정보**
  - *[객체 라벨, 객체 중심 좌표(x,y), 객체 가로길이(w), 객체 세로길이(h)]*
  - 객체 라벨은 0 부터 시작하는 정수이며 위치 정보는 0 ~ 1 사이의 값을 갖음
  - 파일은 txt 파일 형식으로 이미지 파일명과 동일함 (파일 확장자 제외)
  - 편의를 위해 해당 txt 파일을 yolo format txt 파일이라 하겠음 <br><br>

  <p align="center">
  🚀 YOLO 형식 객체 위치 정보 🚀
  </p>
  <p akkugb-"cnter">
  [객체 라벨, 객체 중심 좌표(x,y), 객체 가로길이(w), 객체 세로길이(h)]
  </p>
  <p align="center">
  객체 라벨은 0 부터 시작하는 정수이며 위치 정보는 0 ~ 1 사이의 값을 갖음
  </p>
  <p align="center">
  파일은 txt 파일 형식으로 이미지 파일명과 동일함 (파일 확장자 제외)
  </p>
  <p align="center">
  편의를 위해 해당 txt 파일을 yolo format txt 파일이라 하겠음
  </p>

  #### 2-2. 변환 과정
  - 우리가 사용하는 데이터에서 객체 위치 정보는 Bounding Box의 좌측 상단 좌표(x_min, y_min), 우측 하단 좌표(x_max, y_max)로 제공됨

## 최종 모델
- 모델 개발 기간 내에 개발한 모델(YOLO의 경우 .pt 파일)을 주최측에 메일로 전달하면 3,000 장의 test 이미지로 산출한 모델 정확도(mAP, IoU = 0.5)와 추론 시간(s)이 주기적으로 참가자에게 제공됨
- 제공된 모델 평가 지표로 순위를 매겨 최종 발표 평가에 참가할 10 팀을 선정함
- 모델 평가는 상대 평가로 **정확도 30점, 추론시간 30점, 모델 개발 창의성 부문 40점임**
- 이번 대회에서 **20개의 YOLO 모델을 개발**했으며 최종적으로 모델 평가에 활용될 하나의 모델을 선정해서 제출해야 됐기 때문에
- **정확도와 추론시간에 기반해 하나의 모델을 선정함**


<br>

## YOLO 선택 배경
- YOLO는 You Only Look Once의 약자로 이미지를 한 번만 보고도 예측을 수행하며 객체 검출 성능이 좋은 모델로 알려짐
- 사용자 친화적인 UI를 제공하며 간단한 코드 몇 줄 만으로 모델 학습 및 검증 결과를 알 수 있음

<br>

## 개발 과정
### 1. 객체 위치 정보 정규화
- YOLO 모델 학습을 위해 객체 위치 정보를 0 ~ 1 사이의 값으로 변환하는 과정을 진행
- 객체 위치 정보 파일(.json) 구조 예시
<p align="center">
<img width="333" height="432" alt="Image" src="https://github.com/user-attachments/assets/c8b47ed1-5f2b-4110-9e6b-4ad034b28de7" />
</p>

<br>
<br>

- 변환 결과 예시
<p align="center">
<img width="247" height="130" alt="1" src="https://github.com/user-attachments/assets/e4e16ba8-8d67-4eb5-8e54-528da78cacb0" />
</p>

<br>
<br>

### 2. Base 모델 학습
<p align="center">
<img width="722" height="155" alt="image (2)" src="https://github.com/user-attachments/assets/fbc7bbc1-ffde-4628-a7eb-a4c1463476a4" />
</p>


<br>
<br>

### 3. 최종 모델 학습
<p align="center">
<img width="724" height="71" alt="image" src="https://github.com/user-attachments/assets/92f9c6e6-7bb9-4d52-ab29-cffcf208a011" />
</p>


<p align="center">
<img width="600" height="300" alt="results" src="https://github.com/user-attachments/assets/b93ab859-a1f2-426f-9c90-45c0ac75b1bf" />
</p>


<br>
<br>

### 4. 시연 영상

- [test dataset](https://github.com/user-attachments/assets/715ef921-bc30-4d1c-8d89-b6caf0dff56a)

- [daytime](https://github.com/user-attachments/assets/1867900f-da03-4578-b419-428d62d5cc6e)

- [night](https://github.com/user-attachments/assets/45fd9091-5d0a-4dc5-b9da-cfb9ff2c104a)







 

