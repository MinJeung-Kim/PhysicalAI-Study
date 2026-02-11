# TensorFlow.js 웹캠 이미지 분류기

## 프로젝트 개요
웹 브라우저에서 실시간으로 작동하는 이미지 분류기입니다. TensorFlow.js와 MobileNet 사전학습 모델을 활용한 전이 학습(Transfer Learning)을 통해 사용자가 직접 두 가지 클래스를 학습시키고 실시간으로 분류할 수 있습니다.

## 주요 기능
- 📹 **실시간 웹캠 캡처**: 브라우저에서 직접 웹캠 영상 활용
- 🧠 **전이 학습**: MobileNet 사전학습 모델을 특징 추출기로 사용
- 🎯 **2-클래스 분류**: Class A와 Class B 두 가지 클래스 학습 및 예측
- ⚡ **실시간 예측**: 학습 후 즉시 실시간 분류 수행
- 🌐 **브라우저 기반**: 별도 설치 없이 웹 브라우저에서 실행

## 기술 스택
- **TensorFlow.js v4.16.0**: JavaScript 기반 머신러닝 라이브러리
- **MobileNet**: 이미지 특징 추출을 위한 사전학습 모델
- **HTML5**: video 태그를 통한 웹캠 접근

## 실행 방법
1. `index.html` 파일을 웹 브라우저에서 실행합니다
2. 웹캠 접근 권한을 허용합니다
3. MobileNet 모델이 로딩될 때까지 기다립니다 (콘솔에 "MobileNet Loaded" 표시)

## 실행 화면   
https://github.com/user-attachments/assets/6b37df5b-2fcf-4f63-b81b-9750de02337a


## 사용 방법

### 1. 데이터 수집
- **Class A 학습**: 첫 번째 클래스로 인식시킬 대상을 웹캠에 보여주고 "Class A" 버튼을 여러 번 클릭
- **Class B 학습**: 두 번째 클래스로 인식시킬 대상을 웹캠에 보여주고 "Class B" 버튼을 여러 번 클릭
- 각 클래스마다 10~20개 이상의 샘플 수집 권장

### 2. 모델 학습
- 충분한 샘플을 수집한 후 "Train" 버튼 클릭
- 학습이 완료되면 콘솔에 "Training Complete" 표시

### 3. 실시간 예측
- 학습 완료 후 자동으로 실시간 예측 시작
- 웹캠 영상에 따라 "Class A" 또는 "Class B" 결과 표시

## 코드 구조

### 주요 함수

#### `setup()`
```javascript
async function setup()
```
- 웹캠 초기화 및 스트림 연결
- MobileNet 모델 로드
- 페이지 로드 시 자동 실행

#### `captureImage()`
```javascript
function captureImage()
```
- 현재 웹캠 프레임 캡처
- 이미지를 224x224 크기로 리사이징
- MobileNet을 통해 특징 벡터 추출 (전이 학습)
- `tf.tidy()`로 메모리 누수 방지

#### `addExample(label)`
```javascript
function addExample(label)
```
- 현재 웹캠 이미지의 특징 벡터를 데이터셋에 추가
- `dataset`: 특징 벡터 배열
- `labels`: 레이블(0 또는 1) 배열

#### `train()`
```javascript
async function train()
```
- 수집된 데이터로 신경망 모델 학습 수행
- **모델 구조**:
  - Dense Layer 1: 100 units, ReLU activation
  - Dense Layer 2: 2 units, Softmax activation (출력층)
- **학습 설정**:
  - Optimizer: Adam (learning rate: 0.0001)
  - Loss: Categorical Crossentropy
  - Epochs: 20
  - Metric: Accuracy

#### `predict()`
```javascript
async function predict()
```
- 학습 완료 후 무한 루프로 실시간 예측 수행
- 각 프레임마다 특징 추출 및 분류
- 결과를 화면에 표시
- `tf.nextFrame()`으로 브라우저 렌더링 주기에 맞춰 실행

## 데이터 흐름

```
웹캠 영상 (224x224)
    ↓
MobileNet 특징 추출 (전이 학습)
    ↓
특징 벡터 (1024차원 등)
    ↓
Dense Layer (100 units, ReLU)
    ↓
Output Layer (2 units, Softmax)
    ↓
Class A / Class B 예측
```

## 전이 학습 (Transfer Learning)

이 프로젝트는 **전이 학습** 기법을 사용합니다:

1. **MobileNet (특징 추출기)**: 
   - ImageNet에서 사전학습된 모델
   - 이미지에서 고수준 특징을 추출
   - 가중치는 고정 (학습하지 않음)

2. **Custom Classifier (분류기)**:
   - 2층 신경망만 새로 학습
   - MobileNet이 추출한 특징을 기반으로 학습
   - 적은 데이터로도 빠르고 정확한 학습 가능

## 성능 최적화

- `tf.tidy()`: 중간 텐서 자동 정리로 메모리 누수 방지
- `tf.nextFrame()`: 브라우저 메인 스레드 블로킹 방지
- MobileNet: 경량화된 모델로 빠른 추론 속도

## 개선 가능 사항

1. **클래스 확장**: 2개 이상의 클래스로 확장 가능
2. **모델 저장/로드**: `model.save()` / `tf.loadLayersModel()` 활용
3. **데이터 증강**: 밝기, 회전 등 데이터 증강 기법 추가
4. **UI 개선**: 수집된 샘플 수, 학습 진행률 표시
5. **신뢰도 표시**: 예측 확률값 표시

## 라이선스
오픈소스 프로젝트

## 참고 자료
- [TensorFlow.js 공식 문서](https://www.tensorflow.org/js)
- [MobileNet 모델](https://github.com/tensorflow/tfjs-models/tree/master/mobilenet)
