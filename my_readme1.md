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
2. 安装anaconda3， https://www.jianshu.com/p/e298b9d3afae ，
   wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Linux-x86_64.sh
   bash /home/xiaocuiping/Anaconda3-5.3.1-Linux-x86_64.sh
3. python3.7依然是问题，anaconda的python版本是3.8

    wget https://ftp.nluug.nl/pub/os/Linux/distr/pclinuxos/pclinuxos/srpms/SRPMS.pclos/libffi-3.2.1-2pclos2019.src.rpm
    https://ftp.nluug.nl/pub/os/Linux/distr/pclinuxos/pclinuxos/apt/pclinuxos/64bit/RPMS.x86_64/lib64ffi-devel-3.2.1-2pclos2019.x86_64.rpm
  
### 程序语句
1. python ./PaddleOCR/tools/infer/predict_system.py --image_dir="./doc/imgs/11.jpg" --det_model_dir="./inference/ch_ppocr_mobile_v1.1_det_infer/" --rec_model_dir="./inference/ch_ppocr_mobile_v1.1_rec_infer/" --cls_model_dir="./inference/ch_ppocr_mobile_v1.1_cls_infer/" --use_angle_cls=True --use_space_char=True --use_gpu=true
 

ERROR: Package 'imageio' requires a different Python: 2.7.12 not in '>=3.5'
python3 -m pip install ./PaddleOCR/requirments.txt
python -m pip install -r ./requirments.txt
python3 ./tools/train.py -c "D:\PaddleOCR\configs\det\det_mv3_db_v1.1.yml"

/Projects/PaddleOCR
python3 -V
