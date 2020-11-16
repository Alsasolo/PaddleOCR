# 本地安装运行PaddleOCR
## 软件准备工作
1. 下载Pycharm，Python3.7，git。
2. 根据https://github.com/PaddlePaddle/PaddleOCR/blob/develop/doc/doc_ch/installation.md 指示逐步完成环境配置，主要是用git clone PaddleOCR项目，以及pip所需要的安装包。
3. 下载所需的模型，det检测，cls方向分类，rec识别。
## 出现的问题及解决
1. PaddleOCR对Python版本有严格要求，确保下载3.7及以下，否则出现会找不到模块的报错。
2. 下载各种安装包，安装包的名称在requirement.txt文件里，使用pip，对于不能直接使用“pip 包的名称”成功安装的包，可以自己在网上下载该包的whl文件，再“pip 下载的whl文件的路径”。
   除了shapely，还需要特别注意numpy，numpy不能直接pip成功，一定要找到对应版本的numpy，使用"pip list"可查看已有安装包。可能没有进行任何安装操作，numpy已在pip list里面，但
   还是会报错，因为版本不正确，正确的版本应该是有+mkl的。
3. 用命令行执行predict_system.py,并给定图片，模型路径，可能出现character.py程序中，字典路径的报错，需要把报错路径加转义字符r，对于不同的语言，要切换字典，切换rec模型。
   有支持中英文识别的模型。其他语种的模型只能单独识别该语种，有Germany，Japan，French，Korean。
4. 韩语识别效果极差。
## 程序语句示例
1. python D:\ocr\pic\PaddleOCR\tools\infer\predict_system.py --image_dir="./doc/imgs/11.jpg" --det_model_dir="./inference/ch_ppocr_mobile_v1.1_det_infer/"  --rec_model_dir="./inference/ch_ppocr_mobile_v1.1_rec_infer/" --cls_model_dir="./inference/ch_ppocr_mobile_v1.1_cls_infer/" --use_angle_cls=True --use_space_char=True --use_gpu=False
2. #character_dict_path = config['character_dict_path']
   character_dict_path = r"D:\PaddleOCR\ppocr\utils\dict\korean_dict.txt"
3. pip install/uninstall/list   

# 训练det模型
## 准备工作
1. 参照训练文档 https://github.com/PaddlePaddle/PaddleOCR/blob/develop/doc/doc_ch/detection.md 进行文字检测模型的训练，该文档以icdar2015数据集为例，介绍PaddleOCR中检测模型的训练、评估与测试。
2. 主要包括icdar2015数据集的下载，以及下载MobileNetV3的预训练模型。
## 出现的问题及解决
1. 可以直接获得完整的标签文件，也可以自己使用众多单张图片的标签文件生成单个标签文件。出现了解码译码问题，将命令行形式下的编码方式设置成UTF8，并没有解除报错，也不是不能在命令行形式下运行，只是需要将读写文件时的encoding方式明确，读文件时encoding=，写文件时，即可顺利生成标签文件。
2. 训练模型时，出现报错，没办法正确读取模型文件，以为是预训练模型没能在Windows下正确解压，让朋友帮忙在Linux下按照tar -xf语句解压，但解压效果一样，说明解压文件没问题。
按照报错的地方，各种修改模型路径，事实证明都是无用功，其实把yml配置文件里预训练模型路径修改出绝对路径即可。
3. 使用cpu进行训练，出现爆存，尝试修改参数，减少训练次数，还是爆存。于是开始不得不连上实验室的服务器，可以使用gpu，但使用的是Linux，对操作语句不熟练，尤其是使用mobaxterm远程控制终端，重新搭建环境，对这种形式的操作流程不明确。语句sodu执行出现错误，超级用户权限怎么解决尚未明确。
4. split报错，出现单值赋给多值的错误，简单修改即解除报错。
k = s.split('=')[0]
v = s.split('=')[0]
#k, v = s.split('=')
5. 生成log文件报错，tee无法执行，把tee换成tee.exe文件绝对路径"C:\Program Files\Git\usr\bin\tee.exe"即可。
## 程序语句示例
1. #with open(out_label, 'w') as out_file:
with open(out_label, 'w', encoding='UTF-8') as out_file:
2. #with open(os.path.join(input_dir, label_file), 'r') as f:
with open(os.path.join(input_dir, label_file), 'r', encoding='UTF-8-sig') as f:
3. # ./pretrain_models/MobileNetV3_large_x0_5_pretrained/
pretrain_weights: D:\PaddleOCR\pretrain_models\MobileNetV3_large_x0_5_pretrained\
4. python "D:\PaddleOCR\train_data\gen_label.py" --mode="det" --root_path="icdar_c4_train_imgs/"    --input_path="D:\PaddleOCR\train_data\icdar2015\text_localization\ch4_training_localization_transcription_gt"     --output_label="D:\PaddleOCR\train_data\icdar2015\text_localization\train_icdar2015_label.txt"
5. python "D:\PaddleOCR\tools\train.py" -c "D:\PaddleOCR\configs\det\det_mv3_db_v1.1.yml"    2>&1|"C:\Program Files\Git\usr\bin\tee.exe" train_det.log
