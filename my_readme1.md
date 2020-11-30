# 服务器端部署paddleOCR
本地运行paddleOCR，无法训练模型，服务器端使用gpu进行模型训练。
## 本地文件上传到远程服务器
1. python，paddleOCR文件上传。
2. paddlepaddle-gpu使用pip下载。
3. pip 所有requirements.text 里的安装包。
   python3 -m pip
   pip install ./PaddleOCR/requirments.txt
## 服务器端运行
### 出现的问题及解决
1. 有无数个pip，无数个python，版本又是问题，安装包是问题。必须在服务器端部署python3.7，不能简单本地上传。
   严格安装步骤来部署python3.7，https://blog.csdn.net/hero_myself/article/details/90213476 ，
   make install DESTDIR=/home/xiaocuiping/Python3
   但该方法还是失败了，死在libffi devel上。
2. 安装anaconda3， https://www.jianshu.com/p/e298b9d3afae ，
   wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Linux-x86_64.sh
   bash /home/xiaocuiping/Anaconda3-5.3.1-Linux-x86_64.sh
3. python3.7依然是问题，anaconda的python版本会升级成3.9。所以conda不能随便用。有了conda之后，python，python3都是3.7，pip要用python -m pip要才能对应到3.7的python。
   python -m pip install -r ./requirments.txt
   python -m pip install paddlepaddle-gpu
4. 安装包时依旧会错，numpy版本，不过应该能解决，毕竟有经验。
5. ExternalError:  Cudnn error, CUDNN_STATUS_BAD_PARAM  at (/paddle/paddle/fluid/operators/batch_norm_op.cu:198)
  [operator < batch_norm > error]
  运行该句就能解决 export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBARAY_PATH
## 测试多语言识别效果
1. 多语言时，要更换rec模型，更换字典。
  "ch", 'japan', 'korean', 'french', 'german'
   "/home/xiaocuiping/Projects/PaddleOCR/ppocr/utils/dict/korean_dict.txt"
2. 需要进入conda环境
   cd ./Projects/PaddleOCR
   conda activate base
   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBARAY_PATH
3. python ./tools/infer/predict_system.py --image_dir="/home/xiaocuiping/Projects/PaddleOCR/img/in/ger_2.jpg" --det_model_dir="./inference/ch_ppocr_mobile_v1.1_det_infer" --rec_model_dir="/home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/german_ppocr_mobile_v1.1_rec_infer/german_ppocr_mobile_v1.1_rec_infer" --cls_model_dir="./inference/ch_ppocr_mobile_v1.1_cls_infer" --use_angle_cls=True --use_space_char=True --use_gpu=true 
## 训练模型
 安照文件 https://github.com/PaddlePaddle/PaddleOCR/blob/develop/doc/doc_ch/detection.md ，训练det模型。
 安装文件 https://www.bookstack.cn/read/PaddleOCR/inference.md#b9ggm9 ，生成推理模型。
### 训练det模型
1. python ./tools/train.py -c "./configs/det/det_mv3_db_v1.1.yml" 
   用的icdar2015数据集。
2. 训练模型转inference模型。
   python tools/export_model.py -c configs/det/det_mv3_db_v1.1.yml -o Global.checkpoints=./output/det_db/iter_epoch_1000 Global.save_inference_dir=./output/inference/det_db/
3. 模型使用
   python tools/infer_det.py -c configs/det/det_mv3_db_v1.1.yml -o Global.infer_img="./doc/imgs_en/img_10.jpg" Global.checkpoints="./output/det_db/iter_epoch_1000"
    "/home/xiaocuiping/Projects/PaddleOCR/output/inference/det_db/"
    python ./tools/infer/predict_system.py --image_dir="/home/xiaocuiping/Projects/PaddleOCR/img/in/en_1.jpg" --det_model_dir="/home/xiaocuiping/Projects/PaddleOCR/output/inference/det_db" --rec_model_dir="/home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/german_ppocr_mobile_v1.1_rec_infer/german_ppocr_mobile_v1.1_rec_infer" --cls_model_dir="./inference/ch_ppocr_mobile_v1.1_cls_infer" --use_angle_cls=True --use_space_char=True --use_gpu=true
### 训练rec模型
1. python gen_label.py --mode="rec" --input_path="{path/of/origin/label}" --output_label="rec_gt_label.txt"
2. python3 tools/train.py -c configs/rec/rec_icdar15_train.yml
### 程序语句
1. python ./tools/infer/predict_system.py --image_dir="/home/xiaocuiping/Projects/PaddleOCR/img/in/korean_2.jpg" --det_model_dir="./inference/ch_ppocr_mobile_v1.1_det_infer" --rec_model_dir="/home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/korean_ppocr_mobile_v1.1_rec_infer/korean_ppocr_mobile_v1.1_rec_infer" --cls_model_dir="./inference/ch_ppocr_mobile_v1.1_cls_infer" --use_angle_cls=True --use_space_char=True --use_gpu=true

python ./tools/infer/predict_system.py --image_dir="/home/xiaocuiping/Projects/PaddleOCR/img/in/korean_3.jpg" --det_model_dir="/home/xiaocuiping/Projects/PaddleOCR/output/inference/det_db" --rec_model_dir="/home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/korean_ppocr_mobile_v1.1_rec_infer/korean_ppocr_mobile_v1.1_rec_infer" --cls_model_dir="./inference/ch_ppocr_mobile_v1.1_cls_infer" --use_angle_cls=True --use_space_char=True --use_gpu=true



 /home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/german_ppocr_mobile_v1.1_rec_infer/german_ppocr_mobile_v1.1_rec_infer/model
 "/home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/german_ppocr_mobile_v1.1/german_ppocr_mobile_v1.1_rec_infer/"
  "./doc/imgs/11.jpg""./doc/imgs_en""/home/xiaocuiping/Projects/PaddleOCR/img/in/"
  "/home/xiaocuiping/Projects/PaddleOCR/inference/kjgf/korean_ppocr_mobile_v1.1_rec_infer/korean_ppocr_mobile_v1.1_rec_infer/"
  "./inference/ch_ppocr_mobile_v1.1_rec_infer"
 
 
ERROR: Package 'imageio' requires a different Python: 2.7.12 not in '>=3.5'
python -m pip install -r ./requirments.txt

 python ./tools/train.py -c "./configs/det/det_mv3_db_v1.1.yml"
 No module named 'cv2'
 No module named 'shapely'
 'D:\\PaddleOCR\\configs\\det\\det_db_icdar15_reader.yml'
  No module named 'imgaug'
  No such file or directory: './train_data/icdar2015/text_localization/test_icdar2015_label.txt'
  not find model file path ./inference/ch_ppocr_mobile_v1.1_det_infer//model
  'D:\\PaddleOCR\\ppocr\\utils\\ppocr_keys_v1.txt'


   cpu specific dynamic library is not loaded.
(base) xiaocuiping@amax:~/Projects/PaddleOCR$

  ExternalError:  Cudnn error, CUDNN_STATUS_BAD_PARAM  at (/paddle/paddle/fluid/operators/batch_norm_op.cu:198)
  [operator < batch_norm > error]
  export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBARAY_PATH
  
  : DeprecationWarning: Using           or importing the ABCs from 'collections' instead of from 'collections.abc' is deprecated, and in 3.8 it will stop worki
  
  Cudnn error, CUDNN_STATUS_BAD_PARAM  at (/paddle/paddle/fluid/operators/batch_norm_op.cu:319)
  [operator < batch_norm > error]
  
  TensorRT dynamic library (libnvinfer.so) that Paddle depends on is not configured correctly. (error code is libnvinfer.so: cannot open shared object file: No such file or directory)
  Suggestions:
  1. Check if TensorRT is installed correctly and its version is matched with paddlepaddle you installed.
  2. Configure TensorRT dynamic library environment variables as follows:
  - Linux: set LD_LIBRARY_PATH by `export LD_LIBRARY_PATH=...`



cd ./Projects/PaddleOCR
 python -V
 pip -V
 python -m pip install upgrade pip
