# fmri_prepocessing
fmri data prepocess using python lib


安装fmriprep对fmri数据进行预处理。
可以选择用docker封装好的fmriprep或者自己配置环境、安装依赖。因为我使用python脚本多一些，没有使用过docker，所以选择自己配置环境。
安装fmriprep：
anaconda prompt
pip install fmriprep
在安装的过程中，出现报错：
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory:'d:\\anaconda3\\envs\\neuro\\lib\\site-packages\\certifi-2025.6.15.dist-info\\METADATA'
是因为安装的certifi库中缺少METADATA文件，可能是由于以前安装了该文件的其他版本，在相应的路径下找到该版本文件的METADATA，并复制报错中显示的目标文件夹就可以（参考：https://www.cnblogs.com/huadongw/p/15193265.html）
导入fmriprep时报错
ValueError: cannot find context for 'forkserver'
原因：forkserver只支持Unix系统
解决方法
注：这两行一定要在import fmriprep之前
python
import multiprocessing as mp
mp.set_start_method('spawn')

由于python3默认的编码格式是UTF-8，和库函数的编码格式不兼容，一开始认为是需要修改编码格式，但是发现在终端可以通过python导入有问题的nipype库，在vscode中的jupyter notebook却不可以导入，发现是jupyter notebook和终端的内核不一致，因此删除jupyter notebook的内核，重新安装。
安装好之后目前只能在脚本里运行，暂时没找到原因。
由于nipype的spm库中与原始的spm软件中TPM.nii文件的路径不一致，因此下载TPM.nii文件并放置到目标路径，将tpm_img修改为TPM文件所在的目标路径，否则会因为找不到文件出现报错。
Python
tpm_img ='./TPM.nii'  # specify the path to the TPM image
if not os.path.exists(tpm_img):
    raise FileNotFoundError(f"TPM image not found: {tpm_img}, please check the targeting path!")

在coregistration处理部分，bbr.sch也不在样例给出的路径中，因此在anaconda对应环境的site-packages里搜索bbr.sch文件，发现在之前下载的fmriprep库函数里有这个文件，因此将路径修改到对应的位置。
Python
coreg = Node(FLIRT(dof=6,
                   cost='bbr',
                   # link the path to the bbr.sch file
                   schedule='"D:\\anaconda3\\envs\\neuro\\Lib\\site-packages\\fmriprep\\data\\flirtsch\\bbr.sch"',
                   output_type='NIFTI'),
             name="coreg")

注：运行整个workflow需要保证环境中安装了pwd，pwd是nipype.pipeline.plugins的依赖包。
但是使用conda和pip都没法安装pwd成功，在报错路径下的Lib中放入名为pwd.py的python文件：
Python
from os import *
from pwd import *

def get_username():
    return getpwuid(getuid())[0]
就可以成功导入pwd了
注：程序只有在linux/MacOS环境下可以运行，windows不能运行，会报错，所以环境只能在MacOS或者linux系统（Ubuntu/CentOS，windows系统可以用Windows Subsystem for Linux）。

测试数据集需要用datalad下载，datalad的安装用conda install-c conda-forge datalad就可以（详细数据及文件信息见introduction_dataset）。一定要注意下载数据时后面对应的目录，如果目录错误会报错。可能原版的说明文档和示例是根据程序在根目录（/）下运行得到的结果，所以很多的目录设置都不适用于非根目录运行的情况（大多数时候都不是，并且不建议），所以在程序运行的过程中一定要注意文件目录的设置，否则很容易出现很多关于文件名的报错。
bash shell
cd /data
datalad install -r ///workshops/nih-2017/ds000114
cd /ds000114
datalad get -J 4 ./derivatives/fmriprep/sub-*/anat/*preproc.nii.gz
datalad get -J 4 ./sub-01/ses-test/anat
datalad get -J 4 ./sub-*/ses-test/func/*fingerfootlips*

注：运行程序需要freesurfer软件，否则会出现报错
