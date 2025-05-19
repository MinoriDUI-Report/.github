# 📋 12주차 작업 보고서

## 🧩 개요

이번 주차에는 기존에 구축해둔 pose, EAR, velocity 기반의 feature vector(`augmented.npz`)를 활용하여 시계열 분류 모델(LSTM)을 학습하고, ONNX 형식으로 export하였습니다. 이후 `.mp4` 기반 실제 영상에서 추출한 feature에 대해 추론을 수행하고, 결과를 시각화하였습니다. 이 과정을 통해 향후 웹 기반 데모 개발을 위한 기반을 마련하였습니다.

---

## ✅ 주요 작업

### 1. LSTM 기반 분류기 학습 및 평가

- 입력 feature:  
  - **pose** (17 joints)  
  - **EAR** (eye aspect ratio)  
  - **velocity** (movement speed)  
- 전체 feature dimension: `19`
- **sliding window** 적용: `length=10`, `stride=2`
- 모델 구조:
  - LSTM (hidden size=64, 1 layer)
  - Fully-connected layer → 2-class classification
- 학습 결과 (10 epoch 기준):
  - Accuracy: **84.8%**
  - Recall: **84.8%**
  - Precision: **100%**

- ONNX export 경로:
  `/Users/jiwonkim/Desktop/GradProj/onnx/lstm_model.onnx`

---

### 2. 실시간 추론용 프레임 처리 및 테스트

- `.mp4` 기반 영상에서 프레임을 추출한 후, 기존 파이프라인 (YOLOv10n + MobileSAM)으로 feature 추출
- pose / EAR / velocity를 시계열로 정리한 뒤 sliding window 적용
- export된 ONNX 모델로 분류 실행
- 결과는 `per-window prediction` 형태로 시각화됨

예시 시각화 (drunk 예측):
![Drunk 예측 시각화](./images/drunk_sample.jpeg)

예시 시각화 (sober 예측):
![Sober 예측 시각화](./images/sober_sample.jpeg)

예시 시각화 (부분 혼합):
![혼합 예측 시각화](./images/mixed_sample.jpeg)

---

## 🔍 실험 결과 요약

- **drunk_01**:
  - Drunk ratio: **0.90**
  - 대부분의 window에서 drunk로 예측됨

- **sober_02**:
  - Drunk ratio: **0.00**
  - 전 window에서 sober로 예측됨

- **sober_01**:
  - Drunk ratio: **1.00**
  - 이상치 발생 → 원본 feature 확인 필요

---

## 🛠️ 개선 방향

- 일부 sober 샘플의 이상 예측 문제 → 데이터 품질 또는 underfitting 가능성 점검
- epoch 수 증가 및 dropout 추가 등으로 모델 일반화 성능 향상 예정
- ONNX 추론 결과를 기반으로 결과 overlay 및 영상 출력 기능 추가 예정 (13주차)

---

## 🎯 다음 주 계획 (13주차)

- drunk/sober 결과를 `.mp4` 영상 위에 overlay
- Streamlit 또는 Flask 기반 웹 데모 UI 구성
- 입력 영상 선택 → 추론 결과 시각화 → 출력 영상 저장 기능 구현
