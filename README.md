# Android_Objectdetection

## Model 수정하기
1. [YOLOV5](https://github.com/ultralytics/yolov5)의 export.py에서 코드 수정
   - f = file.with_suffix('.torchscript.pt') 뒤에 fl = file.with_suffix('.torchscript.ptl') 추가
   - (optimize_for_mobile(ts) if optimize else ts).save(f) 뒤에 (optimize_for_mobile(ts) if optimize else ts)._save_for_lite_interpreter(str(fl)) 추가
2. 수정된 모델 파일 저장
   python export.py --weights yolov5s.pt --include torchscript  
