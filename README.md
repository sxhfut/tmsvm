# tmsvm
Automatically exported from code.google.com/p/tmsvm
从code.google.com/p/tmsvm复制。
简介
文本挖掘无论在学术界还是在工业界都有很广泛的应用场景。而文本分类是文本挖掘中一个非常重要的手段与技术。现有的分类技术都已经非常成熟，SVM、KNN、Decision Tree、AN、NB在不同的应用中都展示出较好的效果，前人也在将这些分类算法应用于文本分类中做出许多出色的工作。但在实际的商业应用中，仍然有很多问题没有很好的解决，比如文本分类中的高维性和稀疏性、类别的不平衡、小样本的训练、Unlabeled样本的有效利用、如何选择最佳的训练样本等。这些问题都将导致curve of dimension 、 过拟合等问题。 这个开源系统的目的是集众人智慧，将文本挖掘、文本分类前沿领域效果非常好的算法实现并有效组织，形成一条完整系统将文本挖掘尤其是文本分类的过程自动化。该系统提供了Python和Java两种版本。

主要特征
该系统在封装 libsvm 、 liblinear 的基础上，又增加了 特征选择 、 LSA特征抽取 、 SVM模型参数选择 、 libsvm格式转化模块 以及一些实用的工具。其主要特征如下：

封装并完全兼容*libsvm、liblinear。
基于Chi*的feature selection 见 feature_selection
基于Latent Semantic Analysis 的feature extraction 见 feature_extraction
支持Binary,Tf,log(tf),Tf*Idf,tf*rf,tf*chi等多种特征权重 见 feature_weight
文本特征向量的归一化 见 Normalization
利用交叉验证对SVM模型参数自动选择。 见 SVM_model_selection
支持macro-average、micro-average、F-measure、Recall、Precision、Accuracy等多种评价指标 见evaluation_measure
支持多个SVM模型同时进行模型预测
采用python的csc_matrix支持存储大稀疏矩阵。
引入第三方分词工具自动进行分词
将文本直接转化为libsvm、liblinear所支持的格式。
使用该系统可以做什么
对文本自动做SVM模型的训练。包括Libsvm、Liblinear包的选择，分词，词典生成，特征选择，SVM参数的选优，SVM模型的训练等都可以一步完成。
利用生成的模型对未知文本做预测。并返回预测的标签以及该类的隶属度分数。可自动识别libsvm和liblinear的模型。
自动分析预测结果，评判模型效果。计算预测结果的F值、召回率、准确率、Macro,Micro等指标，并会计算特定阈值、以及指定区间所有阈值下的相应指标。
分词。对文本利用mmseg算法对文本进行分词。
特征选择。对文本进行特征选择，选择最具代表性的词。
SVM参数的选择。利用交叉验证方法对SVM模型的参数进行识别，可以指定搜索范围，大于大数据，会自动选择子集做粗粒度的搜索，然后再用全量数据做细粒度的搜索，直到找到最优的参数。对libsvm会选择c,g(gamma)，对与liblinear会选择c。
对文本直接生成libsvm、liblinear的输入格式。libsvm、liblinear以及其他诸如weka等数据挖掘软件都要求数据是具有向量格式，使用该系统可以生成这种格式：label index:value
SVM模型训练。利用libsvm、liblinear对模型进行训练。
利用LSA对进行Feature Extraction*，从而提高分类效果。
开始使用

QuickStart里面提供了方便的使用指导

如何使用
该系统可以在命令行（Linux或cmd中）中直接使用，也可以在程序通过直接调用源程序使用。 在程序中使用。

#将TMSVM系统的路径加入到Python搜索路径中
import sys
sys.path.insert(0,yourPath+"\tmsvm\src")
import tms

#对data文件夹下的binary_seged.train文件进行训练。
tms.tms_train(“../data/binary_seged.train”) 

#利用已经训练好的模型，对对data文件夹下的binary_seged.test文件预测
tms.tms_predict(“../data/binary_seged.test”,”../model/tms.config”)

#对预测的结果进行分析，评判模型的效果
tms. tms_analysis(“../tms.result”)
在命令行中调用

#对data文件夹下的binary_seged.train文件进行训练。
$python auto_train.py [options]  ../data/binary_seged.train

#利用已经训练好的模型，对对data文件夹下的binary_seged.test文件预测
python predict.py ../data/binary_seged.train ../model/tms.config

#对预测的结果进行分析，评判模型的效果
$python result_anlaysis.py ../tms.result
上面的调用形式都是使用系统中默认的参数，更具体、灵活的参数见程序调用接口

输入格式
label value1 [value2]

其中label是定义的类标签，如果是binary classification，建议positive样本为1，negative样本为-1。如果为multi-classification。label可以是任意的整数。
其中value为文本内容。
label 和value以及value1 和value2之间需要用特殊字符进行分割，如”\t”
模型输出
模型结果会放在指定保存路径下的“model”文件夹中，里面有3个文件，默认情况下为dic.key 、 tms.model和tms.config 。
其中dic.key为特征选择后的词典；
tms.model为训练好的SVM分类模型;
tms.config为模型的配置文件，里面记录了模型训练时使用的参数。
临时文件会放在“temp”文件夹中。里面有两个文件：tms.param和tms.train。
其中tms.param为SVM模型参数选择时所实验的参数。
tms.train是供libsvm和liblinear训练器所使用的输入格式。
源程序说明
src:即该系统的源代码，提供了5个可以在Linux下可以直接调用的程序:auto_train.py、train.py、predict.py为在Linux下通过命令行调用的接口。
tms.py 为在程序中调用的主文件，直接通过import tms 即可调用系统的所有函数。其他文件为程序中实现各个功能的文件。
lsa_src：LSA模型的源程序。
dependence:系统所依赖的一些包。包括libsvm、liblinear、Pymmseg在Linux32位和64位以及windows下的支持包(dll,so文件)。
tools:提供的一些有用的工具，包括result_analysis.py等。
java：java版本的模型预测程序，
项目重要更新日志
2012/09/21 针对linux下的bug进行修正。重新生成win和linux版本的。
2012/03/08 增加stem模块，并修正了几个Bug。
2011/11/22 tmsvm正式发布。
联系方式
邮箱:zhzhl202@163.com

Thanks
本系统引用了libsvm、liblinear的包，非常感谢Chih-Jen Lin写出这么优秀的软件。本系统还引用了Pymmseg，非常感谢pluskid能为mmseg写出Python下可以直接使用的程序

从最初的想法萌生到第一版上线，中间试验了很多算法，最终因为效果不好删掉了很多代码，在这期间得到了许多人的帮助，非常感谢杨铮、江洋、敏知、施平等人的悉心指导。特别感谢丽红一直以来的默默支持。
