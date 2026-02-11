# MediaPipe 손 추적 - 물체 잡기 (Hand Ball AI)

## 📌 프로젝트 개요
MediaPipe Hands를 활용하여 웹캠으로 손을 추적하고, **Pinch 제스처(엄지+검지 집기)**로 화면의 가상 물체를 잡아서 이동시키는 인터랙티브 웹 애플리케이션입니다.

## 🎯 주요 기능
- **실시간 손 추적**: MediaPipe Hands를 통한 21개 손 랜드마크 감지
- **Pinch 제스처 인식**: 엄지와 검지 끝의 거리를 측정하여 집기 동작 감지
- **물체 잡기/놓기**: 손으로 가상 물체를 잡고 이동 가능
- **히스테리시스 적용**: 안정적인 제스처 감지를 위한 이중 임계값 사용

## 🛠 기술 스택
- **MediaPipe Hands**: 손 추적 및 랜드마크 감지
- **HTML5 Canvas**: 비디오 스트림 및 그래픽 렌더링
- **JavaScript**: 제스처 로직 및 물리 처리
- **Camera Utils**: 웹캠 입력 처리

## 📐 핵심 알고리즘

### 1. Pinch 제스처 감지 (히스테리시스)
```javascript
const GRAB_THRESHOLD = 0.05;    // 잡기 시작 임계값
const RELEASE_THRESHOLD = 0.08; // 놓기 임계값

// 엄지(4번)와 검지(8번) 끝 사이의 거리 계산
distance = √((thumbTip.x - indexTip.x)² + (thumbTip.y - indexTip.y)²)

// 히스테리시스 로직
if (!isPinching && distance < 0.05) → isPinching = true
if (isPinching && distance > 0.08) → isPinching = false, grabbed = false
```

**히스테리시스를 사용하는 이유**: 잡기/놓기 임계값을 다르게 설정하여 손 떨림이나 작은 움직임으로 인한 의도치 않은 상태 변화를 방지합니다.

### 2. 물체 잡기 조건
```javascript
// 손과 물체 사이의 거리
distToObject = √((object.x - handX)² + (object.y - handY)²)

// 잡기 조건: Pinch 상태 + 물체와의 거리가 반지름+20 이내
if (isPinching && distToObject < object.radius + 20) {
    object.grabbed = true;
}
```

### 3. 물체 이동
```javascript
// 잡은 상태면 검지 끝 좌표를 따라감
if (object.grabbed) {
    object.x = indexTip.x * canvasWidth;
    object.y = indexTip.y * canvasHeight;
}
```

## 🔧 주요 변수 및 설정

| 변수명 | 값 | 설명 |
|--------|-----|------|
| `GRAB_THRESHOLD` | 0.05 | Pinch 시작 감지 임계값 |
| `RELEASE_THRESHOLD` | 0.08 | Pinch 해제 감지 임계값 |
| `object.radius` | 40 | 물체의 반지름 (픽셀) |
| `maxNumHands` | 1 | 추적할 손의 개수 |
| `modelComplexity` | 1 | 모델 복잡도 (0~2) |
| `minDetectionConfidence` | 0.7 | 손 감지 신뢰도 |
| `minTrackingConfidence` | 0.7 | 손 추적 신뢰도 |

## 📍 손 랜드마크 인덱스
- **4번**: 엄지 끝 (THUMB_TIP)
- **8번**: 검지 끝 (INDEX_FINGER_TIP)

## 🎮 동작 방식

1. **초기화**: 화면 중앙(320, 240)에 청록색 공 생성
2. **손 감지**: MediaPipe가 손을 인식하고 21개 랜드마크 추출
3. **제스처 인식**: 엄지와 검지 사이 거리로 Pinch 상태 판단
4. **물체 잡기**: 
   - Pinch 상태 **AND** 손이 물체 근처(반지름+20px 이내)
   - 조건 만족 시 `object.grabbed = true`
5. **물체 이동**: 잡은 상태에서 손을 움직이면 물체가 따라옴
6. **물체 놓기**: 손가락을 펴면 (`distance > 0.08`) 물체가 제자리에 남음
7. **시각 피드백**: 
   - 잡힌 상태: 빨간색
   - 자유 상태: 청록색


## 실행 화면 
<video width="640" controls>
  <source src="demo.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video> 
 

## 🚀 실행 방법

1. 웹 브라우저에서 `index.html` 열기
2. 웹캠 접근 권한 허용
3. 손을 카메라에 비추기
4. 엄지와 검지를 모아 물체 근처에서 Pinch 동작
5. 손을 움직여 물체 이동
6. 손가락을 펴서 물체 놓기

## 📊 성능 최적화 포인트

- **모델 복잡도 1 사용**: 정확도와 속도의 균형
- **단일 손 추적**: 처리 속도 향상
- **히스테리시스**: 불필요한 상태 변화 감소로 안정성 향상

## 🔍 코드 구조

```
index.html
├── MediaPipe 라이브러리 로드
├── Canvas 설정 (640x480)
├── 물체 객체 정의 {x, y, radius, grabbed}
├── Hands 모델 초기화 및 옵션 설정
├── onResults 콜백
│   ├── 캔버스 초기화 및 비디오 그리기
│   ├── 손 랜드마크 그리기
│   ├── Pinch 거리 계산 (히스테리시스)
│   ├── 손-물체 거리 계산
│   ├── 잡기/놓기 로직
│   └── 물체 렌더링
└── Camera 초기화 및 시작
```

## 💡 활용 가능성
- VR/AR 인터페이스 프로토타입
- 비접촉 UI/UX 시스템
- 교육용 인터랙티브 콘텐츠
- 게임 제어 시스템
- 재활 훈련 애플리케이션

## 🐛 알려진 제한사항
- 손이 화면을 벗어나면 물체가 놓아짐
- 조명 상태에 따라 인식률 변동
- 단일 물체만 지원

## 📝 개선 가능 항목
- [ ] 여러 물체 동시 제어
- [ ] 물리 엔진 적용 (중력, 충돌)
- [ ] 다양한 제스처 추가 (회전, 크기 조절)
- [ ] 손 없을 때 물체 떨어지는 애니메이션
