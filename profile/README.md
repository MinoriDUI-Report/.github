# MinoriDUI-Report Organization

**(KR)**  
MinoriDUI-Report는 홍익대학교 졸업 프로젝트를 위한 Organization이며, 초기 목표는 음주운전 예방 시스템의 프로토타입을 개발하는 것이지만, 장기적으로는 보다 포괄적인 안전 및 범죄 예방 솔루션으로 확장할 수 있는 기반을 마련하는 목표를 지니고 있습니다.

**(EN)**  
MinoriDUI-Report is an organization for a Hongik University graduation project. While the initial goal is to develop a prototype of a drunk-driving prevention system, the long-term objective is to establish a foundation that can expand into broader safety and crime prevention solutions.

---

## 1. 프로젝트 개요 (Project Overview)

**(KR)**  
- **목표:**  
  본 프로젝트는 특정 환경 내에서 이상 행동 및 상황 변화를 실시간으로 감지하여, 잠재적인 위험 신호를 조기에 탐지하고 경고하는 시스템의 가능성을 탐구합니다. 초기 PoC 단계에서는 특정 시나리오를 중심으로 개발하되, 향후 보다 광범위한 안전 및 범죄 예방 솔루션으로 확장할 수 있는 기반을 마련하는 것을 목표로 합니다.

- **핵심 아이디어:**  
  상황별 위험 경고 시스템  
  - 초기 이상 신호 감지 → **경고 알림 1**  
  - 추가 위험 요소 확인 → **경고 알림 2**  
  - 확정적 위험 상황 발생 시 → **최종 경고**

- **PoC 목표:**  
  - Python을 활용한 신속한 프로토타입 구현 및 데모 시연  
  - 필요 시, Rust 기반의 고성능/실시간 처리 모듈 개발 가능성 평가

**(EN)**  
- **Goal:**  
  This project explores the possibility of detecting abnormal behaviors and situational changes in real time within a specific environment, thereby providing early detection and alerts for potential hazards. In the initial PoC phase, we focus on a particular scenario, but aim to lay the groundwork for expanding into more comprehensive safety and crime prevention solutions in the future.

- **Key Idea:**  
  Context-based alert system  
  - Detect initial anomaly → **Alert 1**  
  - Verify additional risk factors → **Alert 2**  
  - Final alert upon confirmed critical risk → **Final Alert**

- **PoC Objective:**  
  - Rapid prototype implementation and demo using Python  
  - If needed, evaluate the feasibility of high-performance/real-time modules with Rust

---

## 2. 기술 스택 (Tech Stack)

**(KR)**  
- **Python:**  
  - 딥러닝 프레임워크: PyTorch, YOLO  
  - 컴퓨터 비전: OpenCV  
  - 웹 데모/API: Flask, FastAPI

- **Edge Computing:**  
  - 임베디드 디바이스(예: Jetson Nano, Raspberry Pi)에서의 실시간 모델 추론  
  - Rust (onnxruntime-rs, opencv crate) 또는 Python(TensorRT, ONNX 등)을 활용한 최적화

**(EN)**  
- **Python:**  
  - Deep Learning Frameworks: PyTorch, YOLO  
  - Computer Vision: OpenCV  
  - Web Demo/API: Flask, FastAPI

- **Edge Computing:**  
  - Real-time inference on embedded devices (e.g., Jetson Nano, Raspberry Pi)  
  - Optimizations with Rust (onnxruntime-rs, opencv crate) or Python (TensorRT, ONNX), depending on performance needs

---

## 3. 레포지토리 구성 (Repository Structure)

**(KR)**  
각 레포지토리는 프로젝트의 특정 영역에 집중하여 체계적인 개발과 협업을 가능하게 합니다:

- **drunk-driving-poc:**  
  프로젝트 전체의 PoC 개요, 데모 실행 및 통합 스크립트를 관리합니다.

- **data-preprocessing:**  
  원본 데이터 수집, 전처리 스크립트, 개인정보 보호(모자이크 처리) 등 데이터 준비 관련 작업을 진행합니다.

- **model-training:**  
  YOLO 및 주취자 감지 모델의 학습, 경량화, ONNX 변환 등의 작업을 담당합니다.

- **edge-inference:**  
  임베디드 디바이스에서의 모델 추론 및 실시간 처리를 위한 Rust 기반 고성능 모듈 개발을 관리합니다.

- **demo-webapp:**  
  Flask 또는 FastAPI를 활용한 웹 데모, 사용자 인터페이스 및 경고 시스템 시연 코드를 포함합니다.

- **deployment:**  
  Docker, Docker Compose, Kubernetes 등을 이용한 배포 및 CI/CD 파이프라인 설정, 인프라 관련 스크립트를 관리합니다.

- **.github:**  
  조직 전반의 GitHub 워크플로우, 이슈/PR 템플릿, 코드 리뷰 가이드 등 공통 정책을 관리합니다.

**(EN)**  
Each repository focuses on a specific aspect of the project, enabling organized development and collaboration:

- **drunk-driving-poc:**  
  Manages the overall PoC overview, demo execution, and integration scripts.

- **data-preprocessing:**  
  Handles raw data collection, preprocessing scripts, and privacy protection (face/plate mosaics).

- **model-training:**  
  Responsible for YOLO and drunk-driving detection model training, optimization, and ONNX conversion.

- **edge-inference:**  
  Develops a high-performance Rust module for real-time inference on embedded devices.

- **demo-webapp:**  
  Contains the web demo (Flask or FastAPI), user interface, and alert system showcase code.

- **deployment:**  
  Manages deployment and CI/CD pipeline settings (Docker, Docker Compose, Kubernetes).

- **.github:**  
  Houses shared GitHub workflows, issue/PR templates, and code review guidelines across the organization.

---

## 4. 라이선스 및 추가 참고 (License & References)

**(KR)**  
- **라이선스:**  
  각 데이터셋 및 코드의 라이선스는 개별 레포지토리 README에 명시됩니다. 데이터셋의 경우, 연구 및 PoC 목적으로만 사용됨을 확인합니다.

- **추가 참고 자료:**  
  - [YOLO 공식 문서](https://pjreddie.com/darknet/yolo/)  
  - [OpenCV 튜토리얼](https://opencv.org/)  
  - [Rust 공식 문서](https://www.rust-lang.org/learn)

**(EN)**  
- **License:**  
  Each dataset and codebase may have its own license, which will be specified in the corresponding repository’s README. All datasets are used strictly for research and PoC purposes.

- **Additional References:**  
  - [YOLO Official Docs](https://pjreddie.com/darknet/yolo/)  
  - [OpenCV Tutorials](https://opencv.org/)  
  - [Rust Official Docs](https://www.rust-lang.org/learn)
