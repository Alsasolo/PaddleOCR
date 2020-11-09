# 本地安装运行PaddleOCR
## 软件准备工作
1. 下载Pycharm，Python3.7，git。
2. 根据https://github.com/PaddlePaddle/PaddleOCR/blob/develop/doc/doc_ch/installation.md 指示逐步完成环境配置，主要是用git clone PaddleOCR项目，以及pip所需要的安装包。
3. 下载所需的模型，det检测，cls方向分类，rec识别。
## 出现的问题及解决
1. PaddleOCR对Python版本有严格要求，确保下载3.7及以下，否则出现会找不到模块的报错。
2. 下载各种安装包，安装包的名称在requirement.txt文件里，使用pip，对于不能直接使用“pip 包的名称”成功安装的包，可以自己在网上下载该包的whl文件，再“pip 下载的whl文件的路径”。
   除了shapely，还需要特别注意numpy，numpy不能直接pip成功，一定要找到对应版本的numpy，使用"pip list"可查看已有安装包。可能没有进行任何安装操作，numpy已在pip list里面，但
   还是会报错，因为版本不正确，正确的版本应该是有mkl的。
3. 用命令行执行predict_system.py,并给定图片，模型路径，可能出现character.py程序中，字典路径的报错，需要把报错路径加转义字符r，对于不同的语言，要切换字典，切换rec模型。
   有支持中英文识别的模型。其他语种的模型只能单独识别该语种，有Germany，Japan，French，Korean。
4. 韩语识别效果极差。
