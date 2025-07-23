# YOLO : 신호등 교차로 객체 탐지
- [제2회 경기도 자율주행센터 자율주행 데이터 활용 경진대회](https://ggzerocity.or.kr/?p=38&page=1&viewMode=view&reqIdx=202408291933131878)에 팀으로 참가해 진행한 프로젝트입니다.
- YOLO 모델의 고질적인 문제인 소형 객체 탐지 성능을 높이는 방법을 고안했으며, 신호등 교차로 이미지를 영상으로 변환해 객체 탐지를 수행합니다.
- 모델 개발 아이디어에서 높은 점수를 받아 [우수상](https://graceful-cello-0d4.notion.site/2-2397d8d98aa880aa84c1d23e3a2132d8?source=copy_link)을 수상했습니다.

<Br>

## 배경
- 학교 팀 프로젝트 과목에서 좋은 학점을 받기 위해 참여함
- 프로젝트를 진행하면서 팀원과의 소통의 중요성, 공모전에서 좋은 점수를 받기 위한 방법, 그리고 YOLO 모델의 단점을 보완할 수 있는 아이디어를 얻게 됨
- [대회 규칙](https://graceful-cello-0d4.notion.site/2397d8d98aa880a29b9ed3f1c4785a08?source=copy_link)

<Br>

## 사용 데이터
- 주최측에서 제공된 한국교통안전공단 ['자율주행 공개데이터셋'](https://challenge.gcontest.co.kr/template/m/frame/downloadlist/16335?q=1368)(40.9GB)
- 1920x1200 크기 도로 주행 이미지 10만장과 객체 10종의 위치 정보 파일(.json) 10만개
  - 학습 데이터 세트 : 80,000 images 
  - 검증 데이터 세트 : 10,000 images 
  - 테스트 데이터 세트 : 10,000 images

- 객체 위치 정보 파일(.json) 속성

  <img width="590" height="182" alt="Image" src="https://github.com/user-attachments/assets/69e214f6-9391-41f0-a44a-8d6c3b80aba1" />

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







 

