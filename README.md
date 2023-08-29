# Android_Objectdetection

## Model 수정하기
1. [YOLOV5](https://github.com/ultralytics/yolov5)의 export.py에서 코드 수정
   - f = file.with_suffix('.torchscript.pt') 뒤에 fl = file.with_suffix('.torchscript.ptl') 추가
   - (optimize_for_mobile(ts) if optimize else ts).save(f) 뒤에 (optimize_for_mobile(ts) if optimize else ts)._save_for_lite_interpreter(str(fl)) 추가
2. 수정된 모델 파일 저장
  ```
   python export.py --weights yolov5s.pt --include torchscript
   ```


## Android Studio File

MainActivity => Kotlin 파일
Method 파일(PrePostProcessor, ResultView) => Java 파일

- assets 폴더 : 객체 인식 모델(food_640_4_500.torchscript.ptl), 라벨 목록 파일(labels_ko.txt)
- PrePostProcessor.java : NMS(임계치 이하의 객체 인식 결과 제거 및 겹치는 객체 인식 Bounding Box 제거)
- ResultView.java : Bitmap에 Bounding box 그리기
  *Bounding box = 객체를 인식한 구역에 그려진 사각형 박스
