# fMRI data preprocessing
- install python lib fmriprep and prepocess the fmri data based on nipype lib. Before prepocessing the data, ensure that you have installed **fsl, spm and matlab**
- 安装fmriprep，基于nipype对fmri数据进行预处理。在对数据进行处理之前，保证安装了fsl，spm，matlab。

-  可以选择用docker封装好的fmriprep或者自己配置环境、安装依赖。因为我使用python脚本多一些，没有使用过docker，所以选择自己配置环境。
## 安装fmriprep：
anaconda prompt
pip install fmriprep
- 测试数据集需要用datalad下载，datalad的安装用conda install-c conda-forge datalad就可以（详细数据及文件信息见introduction_dataset）。一定要注意下载数据时后面对应的目录，如果目录错误会报错。可能原版的说明文档和示例是根据程序在根目录（/）下运行得到的结果，所以很多的目录设置都不适用于非根目录运行的情况（大多数时候都不是，并且不建议），所以在程序运行的过程中一定要注意文件目录的设置，否则很容易出现很多关于文件名的报错。
bash shell
cd /data
datalad install -r ///workshops/nih-2017/ds000114
cd /ds000114
datalad get -J 4 ./derivatives/fmriprep/sub-*/anat/*preproc.nii.gz
datalad get -J 4 ./sub-01/ses-test/anat
datalad get -J 4 ./sub-*/ses-test/func/*fingerfootlips*
- 下载结束以后，如果被试数据文件还是不存在，可以在数据页面单独下载数据进行替换（A test-retest fMRI dataset for motor, language and spatial attention functions.）。
Matlab命令设置对应的spm路径，将spm的文件夹路径输入到default当中：
python
from nipype.interfaces.matlab import MatlabCommand
MatlabCommand.set_default_paths('/home/jyang/spm')
- 然后在终端运行python脚本就可以对数据进行预处理，得到的结果在设置的output文件夹中。
