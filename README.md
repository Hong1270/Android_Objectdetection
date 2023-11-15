# Android_Objectdetection

## Environment
- Android Studio Giraffe | 2022.3.1 Patch 1

<h3 align="left">Languages and Tools:</h3>
<p align="left"> <a href="https://developer.android.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/android/android-original-wordmark.svg" alt="android" width="80" height="80"/> </a> <a href="https://kotlinlang.org" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/kotlinlang/kotlinlang-icon.svg" alt="kotlin" width="80" height="80"/> </a> <a href="https://opencv.org/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/opencv/opencv-icon.svg" alt="opencv" width="80" height="80"/> </a> <a href="https://www.python.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg" alt="python" width="80" height="80"/> </a> <a href="https://pytorch.org/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/pytorch/pytorch-icon.svg" alt="pytorch" width="80" height="80"/> </a> </p>

## Tutorial   (**인공지능 모델 학습 부터 앱 결합 까지**)
### 1. Yolov5 모델 학습
[Yolov5 인공지능 모델 학습 Tutorial 공식 버전](https://github.com/ultralytics/yolov5/blob/master/tutorial.ipynb)

   **3번 Train 참고

### 2. 인공지능 모델 TorchScript lite 버전으로 수정
**Yolov5의 프레임워크인 Pytorch는 모델을 전달(서빙)하기에 아직 적합하지 않기 때문에, Meta가 내놓은 Torchscript를 통해 Pytorch 모델을 최적화하여 앱에 배포함
1. [YOLOV5](https://github.com/ultralytics/yolov5)의 export.py에서 코드 수정
   - f = file.with_suffix('.torchscript.pt') 뒤에 fl = file.with_suffix('.torchscript.ptl') 추가
   - (optimize_for_mobile(ts) if optimize else ts).save(f) 뒤에 (optimize_for_mobile(ts) if optimize else ts)._save_for_lite_interpreter(str(fl)) 추가
2. 수정된 모델 파일 저장
   ```cmd
   python export.py --weights 모델명.pt --include torchscript
   ```
   ```cmd
   python export.py --weights yolov5s.pt --include torchscript
   ```
   => .torchscript.ptl 파일로 변환
### 3. Android Studio + Yolov5
   1. Pytorch Code 참고
   [참고 코드 - Pytorch, yolov5](https://github.com/pytorch/android-demo-app/tree/master/ObjectDetection)

   2. build.gradle 

    <수정 Code>
    minSdk 28
    targetSdk 33
    
    <추가 Code>
    //    Pytorch
        implementation 'org.pytorch:pytorch_android_lite:1.12.2'
        implementation 'org.pytorch:pytorch_android_torchvision_lite:1.12.2'
   3. assets 폴더 생성(app/src/main/assets)
      아래의 하위 파일을 해당 폴더로 이동
      - .torchscript.ptl (사용자 학습 인공지능 모델 파일)
      - .txt (label명이 들어있는 파일)
   4. java, kotlin 파일 코드 수정
      라벨 유형에 맞게 수정

---
## Android Studio Files
[MainActivity => Kotlin 파일]

[Method 파일(PrePostProcessor, ResultView) => Java 파일]

### MainActivity
- 인공지능(AI) 모델을 불러와 객체 인식에 필요한 이미지, 라벨명 파일 읽어서 전달하는 전반적인 업무를 수행하는 Kotlin 파일

### PrePostProcessor
- 2개의 Class로 이루어져 객체 인식 결과를 처리하는 Java 파일

     - PrePostProcessor, NMS
     
       *NMS(None Max Suppression) = 임계치 이하의 객체 인식 결과 제거 및 겹치는 객체 인식 Bounding Box 제거
     - Result, 결과(라벨번호, 정확도(신뢰도), Bounding box 위치) 정보를 가지는 객체
       

  <수정> 라벨 목록이 변경될 경우 아래의 코드에서 58 부분을 (라벨 개수 + 5)의 숫자로 변경
  ```
  private static int mOutputColumn = 58; // left, top, right, bottom, score and 53 class probability
  ```

### ResultView
- 음식 인식 결과(객체 인식 결과)에 대해 Bitmap에 Bounding box 그리는 Java 파일

   *Bounding box = 객체를 인식한 구역에 그려진 사각형 박스

### assets
- 인공지능(AI) 모델 파일 & 추측할 라벨명 리스트 파일이 위치한 폴더(전체 경로: app/src/main/assets/)

   - (현재 사용) food_640_4_500.torchscript.ptl, 객체 인식 인공지능(AI) 모델
   - labels_ko.txt, 한국어로 된 음식 라벨 목록


## 인공지능(AI) 모델
### 모델 설명
Yolov5의 작은 모델 중 작은 크기의 yolov5s.pt 파일을 이용하여 사용자 지정 학습(Fine-Tuning) 수행
- size: 28MB
- 신경망 구조

  Backbone과 Head로 구성
  1. Backbone: 이미지로부터 feature map 추출

     depth_multiple: 0.33  # model depth multiple
     width_multiple: 0.50  # layer channel multiple
     depth와 width에 따라 모델의 크기가 결정됨(dpth-깊이, width-옆으로 Conv계층 개수)
     *feature map(특성맵) = 특징을 찾아내는 맵(입력으로 부터 커널을 사용한 Conv 연산을 통해 나온 결과)
  1. Head: feature map을 바탕으로 물체의 위치 찾기 > 처음 Anchor Box 설정하고 이를 이용해서 Bounding Box 생성
``` yaml
# YOLOv5 🚀 by Ultralytics, AGPL-3.0 license

# Parameters
nc: 53  # number of classes
depth_multiple: 0.33  # model depth multiple
width_multiple: 0.50  # layer channel multiple
anchors:
  - [10,13, 16,30, 33,23]  # P3/8
  - [30,61, 62,45, 59,119]  # P4/16
  - [116,90, 156,198, 373,326]  # P5/32

# YOLOv5 v6.0 backbone
backbone:
  # [from, number, module, args]
  [[-1, 1, Conv, [64, 6, 2, 2]],  # 0-P1/2
   [-1, 1, Conv, [128, 3, 2]],  # 1-P2/4
   [-1, 3, C3, [128]],
   [-1, 1, Conv, [256, 3, 2]],  # 3-P3/8
   [-1, 6, C3, [256]],
   [-1, 1, Conv, [512, 3, 2]],  # 5-P4/16
   [-1, 9, C3, [512]],
   [-1, 1, Conv, [1024, 3, 2]],  # 7-P5/32
   [-1, 3, C3, [1024]],
   [-1, 1, SPPF, [1024, 5]],  # 9
  ]

# YOLOv5 v6.0 head
head:
  [[-1, 1, Conv, [512, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 6], 1, Concat, [1]],  # cat backbone P4
   [-1, 3, C3, [512, False]],  # 13

   [-1, 1, Conv, [256, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 4], 1, Concat, [1]],  # cat backbone P3
   [-1, 3, C3, [256, False]],  # 17 (P3/8-small)

   [-1, 1, Conv, [256, 3, 2]],
   [[-1, 14], 1, Concat, [1]],  # cat head P4
   [-1, 3, C3, [512, False]],  # 20 (P4/16-medium)

   [-1, 1, Conv, [512, 3, 2]],
   [[-1, 10], 1, Concat, [1]],  # cat head P5
   [-1, 3, C3, [1024, False]],  # 23 (P5/32-large)

   [[17, 20, 23], 1, Detect, [nc, anchors]],  # Detect(P3, P4, P5)
  ]
```

### 모델 반환 값 

![이미지](https://user-images.githubusercontent.com/82353544/279893380-92b36bbd-512b-4dcd-8d5c-eff08f071e42.png)

<img width="361" alt="image" src="https://github.com/Hong1270/Android_Objectdetection/assets/82353544/979a6fcb-a1b3-48cf-9216-15756a9b9666">

## 음식 객체 인식 결과 화면
제육볶음 이미지 테스트
<img width="374" alt="image" src="https://github.com/Hong1270/Android_Objectdetection/assets/82353544/a1ffb5e1-6bfc-4205-afdc-27e3c2fbbc08">

[참고 코드 - Pytorch, yolov5](https://github.com/pytorch/android-demo-app/tree/master/ObjectDetection)

[참고 - Yolov5s 구조](https://kxmjhwn.tistory.com/271)
