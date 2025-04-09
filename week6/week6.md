# 6주차 주간 보고서

## 1. 개요
이번 주에는 영상 전처리 단계 및 멀티모달 주취자 판단을 위한 Feature 추출 파이프라인을 구현하였습니다.  
구체적으로, 로컬에 저장된 영상 파일에서 프레임을 추출한 후 아래의 Feature들을 추출하는 코드를 작성하였습니다.
- **Face Feature**: mediapipe를 활용하여 얼굴의 주요 landmark 기반 지표(예, 눈 깜빡임 정도 등)를 계산.
- **Pose Feature**: Pose estimation 알고리즘(예, mediapipe의 Pose 솔루션 또는 YOLOv8-Pose 등)을 이용하여 17개 관절의 (x, y) 좌표(총 34차원 벡터)를 추출.
- **Velocity Feature**: 프레임 간 동일 객체(사람)의 위치 변화(예: 바운딩박스 중심 좌표)를 기반으로 이동 속도를 계산.

이러한 Feature들은 향후 LSTM 또는 멀티모달 모델의 입력으로 사용되어 주취자(Drunk)와 비주취자(Sober)를 분류하는 데 활용될 예정입니다.


---

## 2. 진행 내용
1. **영상 전처리 (Frame Extraction)**  
   - 로컬 영상 파일에서 일정 간격(예, 3프레임마다)으로 프레임을 추출하여 저장.
   - 추출된 프레임은 후속 Feature 추출 단계의 입력 데이터로 활용.

2. **Face Feature 추출**  
   - mediapipe의 Face Mesh 솔루션을 이용하여 얼굴 landmark를 추출하고,
   - 예시로 왼쪽 눈의 landmark 좌표로 Eye Aspect Ratio(눈 깜빡임 정도)를 계산.

3. **Pose Feature 추출**  
   - (예시 코드) mediapipe의 Pose 솔루션을 사용하거나 YOLOv8-Pose, MMPose 등의 모델을 통해 17개 관절의 (x, y) 좌표를 추출.
   - 추출 결과는 각 프레임마다 34차원 벡터로 저장 (실제 코드에서는 해당 라이브러리를 활용할 수 있도록 구성).

4. **Velocity Feature 추출**  
   - 전처리된 프레임에서 동일 객체(예: 사람)의 바운딩박스 중심 좌표를 이용하여,
   - 프레임 간 이동 거리를 계산, 이를 통해 이동 속도(또는 속도 변화량)를 산출.

---

## 3. 향후 계획
- **7주차 작업:**
  - 멀티모달 모델 학습 및 성능 비교
  - 차량 접근 이벤트 로직 테스트 및 구현  

---

## 5. 추가 조사 내역: Edge Device 및 MicroSD 카드 준비

- **Edge Device 조사:**  
  - **NVIDIA Jetson Nano**는 저전력, 고성능 GPU(128-core Maxwell) 탑재와 TensorRT 지원을 통해 실시간 딥러닝 추론에 최적화되어 있습니다.  
  - 소형 폼팩터와 확장 인터페이스(USB 3.0, HDMI, MIPI CSI 등)를 제공하여, 다양한 센서와 주변 장치와 쉽게 연결 가능합니다.
  
- **MicroSD 카드 조사:**  
  - 추천 제품: **렉사 하이퍼포먼스 microSDXC**  
    - **용량:** 128GB  
    - **속도:** 최대 읽기 100MB/s, 최대 쓰기 45MB/s, UHS-I 633x 등급  
  - 이 사양은 Jetson Nano의 운영체제 부팅 및 데이터 I/O에 적합하며, 안정적인 성능과 충분한 저장 용량을 제공합니다.
