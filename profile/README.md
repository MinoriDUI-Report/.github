# 📅 13주차 작업 보고서

## 🔧 주요 작업 개요

이번 주차에는 12주차까지 구축된 추론 파이프라인을 웹 애플리케이션 형태로 제공하기 위해,  
**Flask 기반 서버 개발**과 **overlay 영상 생성 기능 통합**을 완료했습니다.

사용자가 `.mp4` 영상을 업로드하면, 백엔드에서 자동으로 drunk/sober 추론을 수행하고,  
결과를 **overlay된 영상과 drunk ratio 수치**로 웹에 출력할 수 있는 구조로 설계하였습니다.

---

## ✅ 작업 상세

### 1. overlay 영상 생성 기능 개발

- ONNX 추론 결과(시퀀스 기반 drunk/sober 예측)를 기반으로,
  **YOLO 탐지 결과와 함께 overlay된 시각적 영상 출력** 기능 구현
- YOLO로 차량/사람/차 문 등 탐지 → 추론 결과와 결합하여 상태별 색상 표시
- `OpenCV`로 실시간 bounding box 및 텍스트 오버레이 구현
- 최종 영상은 `.mp4`로 저장

### 2. Flask 서버 구축

- Python Flask 프레임워크로 웹 백엔드 서버 구성
- `/` : 영상 업로드용 index 페이지 라우팅
- `/upload` : 업로드된 영상 처리 및 결과 렌더링
- `/results/<filename>` : overlay 영상 서빙 경로

### 3. 추론 파이프라인 통합 (`run_inference_pipeline`)

- 전체 추론 프로세스를 하나의 함수로 통합:
  1. 프레임 추출
  2. pose / face / velocity feature 추출
  3. 시퀀스 생성
  4. ONNX 모델 추론
  5. overlay 영상 생성
  6. **FFmpeg로 브라우저 호환 `.mp4` 포맷으로 변환**

### 4. 브라우저 재생 호환성 확보

- OpenCV가 생성한 `.mp4`는 일부 브라우저에서 재생 오류 발생
- `ffmpeg`를 이용해 H.264 + AAC 코덱으로 변환하고 `+faststart` 옵션 적용
- 최종적으로 **브라우저에서 바로 재생 가능한 영상 파일 제공**

---

## 💻 개발 구조 요약

- `/app.py` : Flask 웹 서버 메인 파일
- `/core/service/inference_pipeline.py` : 전체 추론 및 overlay 생성 통합 로직
- `/templates/index.html`, `result.html` : 업로드 / 결과 웹 페이지 템플릿
- `/static/uploads/` : 업로드된 원본 영상 저장 위치
- `/static/results/` : 추론 후 overlay된 결과 영상 저장 위치

---

## ⏱️ 성능 요약 (테스트 기준)

| 항목 | 평균 소요 시간 |
|------|----------------|
| 프레임 추출 | 1.2초 |
| feature 추출 (Mediapipe) | 4.2초 |
| 시퀀스 생성 | < 0.1초 |
| ONNX 추론 | < 0.1초 |
| Overlay + 변환 | ~11초 |
| **총합** | 약 16–17초 |

---

## 🔍 다음 주차 계획 (14주차)

- 전체 Flask 앱 마무리:
  - 결과 페이지 디자인 개선
  - 분석 중 로딩 UI 추가
- 템플릿 구조 정리 및 배포용 프로젝트 폴더 구성
- 영상 외에도 예측 수치 그래프, 통계 요약 시각화 검토
