# Android_Objectdetection

## Environment
- Android Studio Giraffe | 2022.3.1 Patch 1

<h3 align="left">Languages and Tools:</h3>
<p align="left"> <a href="https://developer.android.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/android/android-original-wordmark.svg" alt="android" width="80" height="80"/> </a> <a href="https://kotlinlang.org" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/kotlinlang/kotlinlang-icon.svg" alt="kotlin" width="80" height="80"/> </a> <a href="https://opencv.org/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/opencv/opencv-icon.svg" alt="opencv" width="80" height="80"/> </a> <a href="https://www.python.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg" alt="python" width="80" height="80"/> </a> <a href="https://pytorch.org/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/pytorch/pytorch-icon.svg" alt="pytorch" width="80" height="80"/> </a> </p>

## Tutorial   (**인공지능 모델 학습 부터 앱 결합 까지**)
### 1. Yolov5 모델 학습
[Yolov5 인공지능 모델 학습 Tutorial 공식 버전] (https://github.com/ultralytics/yolov5/blob/master/tutorial.ipynb)

   => 3번 Train 참고
### 2. 인공지능 모델 TorchScript lite 버전으로 수정
1. [YOLOV5](https://github.com/ultralytics/yolov5)의 export.py에서 코드 수정
   - f = file.with_suffix('.torchscript.pt') 뒤에 fl = file.with_suffix('.torchscript.ptl') 추가
   - (optimize_for_mobile(ts) if optimize else ts).save(f) 뒤에 (optimize_for_mobile(ts) if optimize else ts)._save_for_lite_interpreter(str(fl)) 추가
2. 수정된 모델 파일 저장
   ```cmd
   python export.py --weights yolov5s.pt --include torchscript
   ```
### 3. Android Studio + Yolov5
   1. Pytorch Code Clone
   2. assets 폴더 생성
      - .torchscript (사용자 학습 인공지능 모델 파일)
      - .txt (label명이 들어있는 파일)
   3. 
      라벨 유형에 맞게 수정

## Android Studio Files

MainActivity => Kotlin 파일
Method 파일(PrePostProcessor, ResultView) => Java 파일

- assets 폴더 : 객체 인식 모델(food_640_4_500.torchscript.ptl), 라벨 목록 파일(labels_ko.txt)
- PrePostProcessor.java : NMS(임계치 이하의 객체 인식 결과 제거 및 겹치는 객체 인식 Bounding Box 제거)
- ResultView.java : Bitmap에 Bounding box 그리기
  *Bounding box = 객체를 인식한 구역에 그려진 사각형 박스

  ### PrePostProcessor
  아래의 코드에서 58 부분을 (라벨 개수 + 5)의 숫자로 변경
  ```
  private static int mOutputColumn = 58; // left, top, right, bottom, score and 53 class probability
  ```

## 결과 화면
[<img width="374" alt="image" src="https://github.com/Hong1270/Android_Objectdetection/assets/82353544/a1ffb5e1-6bfc-4205-afdc-27e3c2fbbc08">]

[참고 코드 - Pytorch, yolov5](https://github.com/pytorch/android-demo-app/tree/master/ObjectDetection)
