  TensorFlow的models分为official resarch和community
    1. official
      使用最新的TensorFlow 2的高级API的SOTA模型的例子集合
      由TensorFlow官方维护，支持并保持最新的TensorFlow 2 API
      经过合理优化的性能加速，同时仍易于阅读
    2. research
      研究人员在TensorFlow 1或2中实现的研究模型
      由研究人员维护和支持
    3. community
      由TensorFlow 2支持的机器学习模型和实现的GitHub存储库的精选列表
  我们重点关注official https://github.com/tensorflow/models/tree/master/official
  
  Tensorflow的model实现两大类功能计算机视觉和自然语言处理,我们就关注计算机视觉吧，这个又分为图像分类和对象检测与分割两种，我们需要使用后一种，对象检测提供了两种算法，看着都很强大:
    RetinaNet(密集物体检测的焦点损失)和Mask R-CNN

  首先安装anaconda,这个直接下载安装就行，下载速度不快,参考https://docs.anaconda.com/anaconda/install/
  其次安装tensorflow(最好gpu，否则很慢,先nvidia的显卡对应的CUDA):
    conda create -n tf-gpu tensorflow-gpu
    conda activate tf-gpu
  安装models：
    在项目tf_train目录下运行git clone https://github.com/tensorflow/models.git
    下载了一个models目录
  安装models的依赖
    pip install -r models/official/requirements.txt
  这时候提示错误:cl: 命令行 error D8021 :无效的数值参数“/Wno-cpp”
  打开requirements.txt发现最后一行+https://github.com/cocodataset/cocoapi#egg=pycocotools&subdirectory=PythonAPI

  导致这个错误的原因是因为pycocotools不支持windows。
  但是有大神已经修改出了windows版本，https://github.com/philferriere/cocoapi。
  把requirements.txt中最后这一行删除,然后执行:
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
  安装成功。
