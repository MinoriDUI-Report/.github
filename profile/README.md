# 11주차 작업 보고서

## 개요

본 주차에서는 기존 데이터 전처리 파이프라인 및 feature extraction 과정의 전면적인 개선을 수행하였다. 또한 모델 성능을 향상시키기 위해 최신 object detection 및 segmentation 모델을 기존 pipeline에 새롭게 도입하였다. 이를 통해 downstream task에서 사용할 수 있는 더 정확하고 일관된 feature vector를 확보하고, 향후 multimodal 모델 입력 데이터의 품질을 높이는 기반을 마련하고자 했다. 또한, Vision-Language Multimodal 모델인 **MiniGPT-4**의 실험 환경을 구성하고, 실제 pretrained 모델을 기반으로 간단한 inference를 수행하는 데에 중점을 두었다. 이를 통해 향후 feature vector와 연계된 multimodal 입력 실험 기반을 마련하였다.

## 주요 작업 내용

### 1. YOLOv8n → YOLOv10n 업그레이드

- 기존 object detection 모델로 사용하던 **YOLOv8n**을 최신 버전인 **YOLOv10n**으로 업그레이드하였다.
- YOLOv10n은 YOLOv8 계열 대비 경량화된 architecture와 성능 최적화를 반영하고 있으며, inference 속도 및 detection precision 면에서 개선된 결과를 제공함을 확인했다.
- 모델 업그레이드 과정에서는 pretrained weight를 공식 repository로부터 다운로드하고, 기존 inference script와의 호환성을 검토 및 수정하였다.
- 동일 test image에 대해 YOLOv8n과 YOLOv10n의 inference 결과를 비교하여 bounding box accuracy와 detection latency의 개선을 수치 및 시각적으로 확인하였다.
- 업그레이드된 모델은 향후 MobileSAM과의 연계 pipeline에 적용되었다.

### 2. MobileSAM 도입

- segmentation 모델로는 새롭게 등장한 **MobileSAM**을 도입하였다.
- MobileSAM은 lightweight segmentation model로서, mobile 환경과 edge computing에 적합하도록 설계된 최신 모델이다.
- 기존 pipeline에서는 segmentation에 manual mask 혹은 대체 방식을 사용하였으나, 이번 도입을 통해 YOLOv10n detection output을 기반으로 **segmentation mask를 자동 생성**할 수 있게 되었다.
- MobileSAM의 pretrained weight를 공식 repository에서 다운로드하고, test image에 대한 inference를 진행하여 output mask 품질과 latency를 점검하였다.
- YOLOv10n의 bounding box output → MobileSAM의 ROI input으로 연계하는 pipeline을 추가 구현하여 detection → segmentation 단계의 자동화 및 연계 검증을 완료하였다.
- segmentation output은 post-processing 단계 (thresholding, morphological operation 등)를 추가하여 향후 feature extraction과 연계 가능성을 실험했다.

### 3. 데이터 전처리 pipeline 리뉴얼

- feature extraction pipeline을 기존 방식에서 **완전히 새롭게 재구성**하였다.
- 주요 변경 사항:
    - **pose feature extraction**: 기존 Mediapipe 기반 코드를 리팩토링하여 효율성을 개선하고 missing data 처리 로직 추가
    - **face feature extraction**: eye aspect ratio (EAR) feature 추출 및 JSON 포맷으로 저장 → downstream task에서 바로 array로 변환 가능하도록 변경
    - **velocity feature extraction**: 프레임 간 좌표 변화량 기반 velocity 계산 로직을 개선하고 CSV export 구조를 통일
- 각 feature는 extraction 시 최소 frame 수를 확인하고, 부족할 경우 경고 메시지를 출력하는 validation 로직을 추가하였다.
- 모든 feature는 동일 frame index에 alignment가 맞도록 trimming 처리 → 데이터 간 temporal misalignment 방지
- feature vector 통합 후 **sliding window 기반 시퀀스 생성 코드**를 작성하여 dataset augmentation 수행 (window length=10, stride=2)
- 최종적으로 pose + face + velocity를 합친 시퀀스 dataset(`augmented.npz`)을 생성하였다.
- 추가적으로 dataset integrity 확인을 위한 **feature completeness/validity check script**를 작성하여 각 sample별 feature file 존재 여부 및 데이터 유효성을 자동 검증하였다.

### 4. MiniGPT-4 환경 구성

- MiniGPT-4 공식 GitHub 저장소 클론
- 의존성 설치 (conda 환경 구성 및 `environment.yml` 활용)
- Python 3.10 이상에서 실행 환경 구축
- inference 실행을 위한 `demo.py`, `minigpt4_eval.yaml` 등 구성 요소 확인

### 5. LLaMA2 7B 모델 다운로드 및 설정

- Hugging Face에서 **meta-llama/Llama-2-7b-chat-hf** 모델에 대한 access 요청 → 승인
- huggingface_hub 라이브러리를 활용하여 Python으로 로컬에 weight 다운로드
- 약 20GB 모델 압축 → Google Drive에 업로드
- Colab에서 압축 해제 및 추론 테스트를 위한 경로 설정

### 6. Colab 환경에서 모델 실행

- Google Drive에 업로드한 모델 zip 파일 마운트 및 압축 해제
- config 파일(`minigpt4_llama2_eval.yaml`) 내 weight 경로 수정
- `demo.py` 실행을 통해 이미지 기반 prompt에 대한 응답 출력 확인
- A100 GPU 환경에서 정상 작동 및 응답 확인

---

## 결과물

- YOLOv10n + MobileSAM 기반 detection & segmentation pipeline 구축 및 검증 완료
- sober / drunk dataset의 모든 sample에 대해 feature extraction pipeline 적용 완료
    - feature 부족하거나 오류 발생 sample은 integrity check script로 식별
- `augmented.npz` (pose + face + velocity 통합 시퀀스 dataset) 생성 완료
- integrity check 결과 → feature 파일 존재 여부 및 각 feature vector length CSV (`feature_integrity_check.csv`) 출력 완료
- downstream model (예: LSTM, MiniGPT-4 등) 입력용 데이터셋 준비 완료
- MiniGPT-4 환경 정상 구축
- LLaMA2 모델 로드 및 Colab 연동 완료
- 간단한 이미지 기반 inference 성공
- 향후 커스터마이징 및 feature vector 연동을 위한 기반 확보

---

## 특이사항

- 일부 sober sample에 대해 frame 수 부족 (10 frame 미만)으로 feature extraction 미완료
    - → sliding window 시퀀스 생성 불가 sample → 증강 대상 제외 처리
- YOLOv10n → MobileSAM 연계 시 image I/O latency 소폭 증가 확인 → batch inference 방안 필요성 고려
- 추출된 feature vector dimension → downstream model input dimension과의 호환성 추가 점검 필요
- **Jetson Nano edge device** 사용 예정이나, 추론 latency 및 메모리 요구사항 미확정
    - → 추후 quantization, pruning 또는 LSTM vs MiniGPT-4 inference 경량화 필요성 검토
- **MiniGPT-4 inference 방식** → edge deployment (로컬 inference) or server API inference → 아직 결정되지 않음
    - 초기에는 **server-side inference + API 호출** 형태로 구현 후, edge compatibility 검토 예정
- multimodal task 종류 → 초기 target은 **classification + captioning** task로 설정

---

## 향후 계획

### **12주차: LSTM 학습 및 전처리 파이프라인 구축**
- `augmented.npz` 기반 LSTM 학습 (PyTorch → ONNX 변환)
- `.mp4` 영상 → 프레임 기반 feature 추출
    - YOLOv10n + MobileSAM → pose / ear / velocity
- sliding window 적용 후 LSTM ONNX 모델로 추론 실행

## 🔧 13주차: 결과 시각화 및 데모 개발
- drunk / sober 결과를 영상에 overlay
- `.mp4`로 결과 영상 생성 or Streamlit 기반 데모 UI 구성

## 🔧 14주차: 마무리 및 시연 준비
- 전체 코드 정리 및 문서화
- Streamlit 또는 Flask 기반 웹 인터페이스 개발

---

## 기타

- 전체 dataset의 feature file 상태를 자동 검증하여 문제 sample 사전 파악
- feature extraction, augmentation, integrity check code에 대한 backup 및 documentation 작성 완료
- 향후 추가 실험/배포 시 dataset 복제 가능하도록 모든 output dataset export 완료

---
