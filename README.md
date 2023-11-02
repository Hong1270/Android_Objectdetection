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
   3. assets 폴더 생성(app/src/main/assets)
      아래의 하위 파일을 해당 폴더로 이동
      - .트
<img width="374" alt="image" src="https://github.com/Hong1270/Android_Objectdetection/assets/82353544/a1ffb5e1-6bfc-4205-afdc-27e3c2fbbc08">

[참고 코드 - Pytorch, yolov5](https://github.com/pytorch/android-demo-app/tree/master/ObjectDetection)
[참고 - Yolov5s 구조](https://kxmjhwn.tistory.com/271)
