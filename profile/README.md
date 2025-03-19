# 1주차 작업 보고서

---

## 1. Baseline vs Mixup 실험 요약

### 목적
- **기존 YOLOv8n 모델**에서 Mosaic 증강(**Baseline**)과 **Mixup 증강**이 객체 검출 성능(mAP@0.5, Validation Loss)에 미치는 영향을 비교

### 개요
- **모델**: `yolov8n.pt (pretrained)`
- **데이터**: Roboflow car_driver_seat (5 classes)
- **Epochs**: 10
- **이미지 크기**: 640
- **비교 대상**: Mosaic-only (Baseline) vs Mosaic+Mixup
- **측정 지표**: mAP@0.5, Validation Loss

### Results

|       Metric       | Baseline (Mosaic) | Mixup (Mosaic+Mixup) | Difference         |
|:------------------:|:-----------------:|:--------------------:|:------------------:|
| **Final mAP@0.5**  |      0.835        |        0.852         |  +0.017 *(미미)*   |
| **Final Val Loss** |      2.547        |        2.537         |  -0.010 *(동일)*   |

![63EDE4DA-5C4A-4816-9A34-E348993E358E_1_105_c](https://github.com/user-attachments/assets/25d38d53-f9fc-4f2c-9039-32636e47d9a5)
![5D211575-854C-4EB6-87DB-546E05937B4E](https://github.com/user-attachments/assets/3909d3de-177f-4286-aa96-674967b8ff3e)

- **Conclusion**  
  - Mixup 증강은 실험 데이터 환경에서 **유의미한 성능 개선 효과 없음**
  - → **Baseline 유지** 결정

---

## 2. Hyperparameter Tuning (train4 – train7)

### 목적
- 학습률(**lr**)과 배치 크기(**batch**)의 조합이 최종 mAP와 Validation Loss에 미치는 영향 비교

### 설정

| Run    | lr      | Batch | Folder              |
|--------|---------|-------|---------------------|
| train4 | 0.001   | 4     | `runs/detect/train4` |
| train5 | 0.001   | 8     | `runs/detect/train5` |
| train6 | 0.0001  | 4     | `runs/detect/train6` |
| train7 | 0.0001  | 8     | `runs/detect/train7` |

### 결과 (Epoch 10 기준)

| Run    | mAP@0.5 | Val Box Loss | Comment                |
|--------|---------|--------------|------------------------|
| train4 | 0.835   | 0.950        | 낮은 성능              |
| train5 | 0.852   | 0.920        | 높은 mAP + 낮은 Loss   |
| train6 | 0.833   | 0.950        | 낮은 성능              |
| train7 | 0.852   | 0.918        | **최고 성능 (선택)**   |

![E50A0A3A-9AC6-413E-BC7B-FC68FE4E69CD](https://github.com/user-attachments/assets/595c1975-28cc-4689-a402-34fc4734d6c5)
![B7AF0282-79E4-41CF-B74D-FD0806040F6F](https://github.com/user-attachments/assets/71dc7ea2-2fc8-4af7-8d76-a13991a268c5)

> **최종 결론**  
> - `train7 (lr=0.0001, batch=8)` 이 **최적** 하이퍼파라미터로 확정

---

## 3. Pipeline Test 결과 (Tracking + Pose)

- **Tracking + Pose 파이프라인 정상 작동 확인**  
- 테스트 영상(`PXL_20241123_090617530_3fps.mp4`, 총 158프레임)
  - 추적된 **4명(Track ID: 2, 4, 10, 19)**  
  - 각 ID별로 6 / 24 / 67 / 13 프레임 분량의 Pose 키포인트 추출
- **Pose keypoint**: 17개 관절 × 2D → (34,) 벡터
- 시퀀스 데이터가 정상 저장됨을 확인 → **2.3 LSTM 학습** 준비 완료

---

## 4. LSTM 기반 분류

4.1 소규모 데이터 실험
	•	90프레임(약 3초) 간격으로 구성된 소규모 시퀀스
	•	약 75% 정확도 달성
```
Epoch 1 loss:0.6917
Epoch 2 loss:0.6785
Epoch 3 loss:0.6749
Epoch 4 loss:0.6660
Epoch 5 loss:0.6564
Epoch 6 loss:0.6457
Epoch 7 loss:0.6335
Epoch 8 loss:0.6190
Epoch 9 loss:0.5403
Epoch 10 loss:0.6650
▶ Test Accuracy: 0.75
```

### 4.2 Confusion Matrix & Classification Report

- **결론**:  
  - 현재 테스트 세트는 클래스 수가 매우 적어 drunk 클래스 샘플이 1개뿐임  
  - 향후 drunk 영상 데이터를 추가하면 모델 성능 개선 가능(현재 75퍼센트)

---

## 결론 및 다음 계획

1. **Baseline vs Mixup**  
   - Mixup 증강은 유의미한 성능 개선 효과가 없어 → **Baseline 유지**

2. **Hyperparameter Tuning**  
   - `train7 (lr=0.0001, batch=8)`가 최종 선정됨

3. **Tracking + Pose 파이프라인**  
   - 정상 작동하여 Pose 시퀀스 데이터 수집에 성공

4. **LSTM 분류**  
   - 소규모 데이터로 약 75% 정확도를 달성하였으나, drunk 데이터 부족으로 Recall 개선이 필요함

### 향후 진행 계획
- **차량 상호작용 이벤트 감지 (2.4 단계)**:  
  - 차량 접근, 운전석 문 열림, 차량 이동 이벤트를 감지하여 화면 색상(노랑/핑크/빨강)으로 시각화
- **데이터 확충**:  
  - 주취자(drunk)와 일반인(sober) 영상 데이터를 각각 추가 확보하여 LSTM 모델을 재학습
- **최종 PoC**:  
  - 전체 파이프라인(Detection → Tracking → Pose → LSTM → 이벤트 감지)을 통합한 PoC 완성
- **추가 계획 (Edge/Rust PoC)**:  
  - 후속 단계로, 학습된 모델을 ONNX 변환하여 Rust 기반 Edge 환경에서의 실시간 추론 PoC 진행 예정

---

> **요약**:  
> 이번 1주차 작업에서는 기본 검출 및 추적, 그리고 소규모 LSTM 분류 PoC를 성공적으로 완료하였으며, 다음 단계에서는 차량 이벤트 감지 및 데이터 확충, 나아가 Edge Computing 적용 계획을 수립할 예정입니다.
