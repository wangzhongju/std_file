***cuda设置指定的GPU可见
	设置环境变量CUDA_VISIBLE_DEVICES，指明可见设备
	在/etc/proflie或~/.bashrc的配置文件中配置环境变量
	(/etc/profile影响所有用户，~/.bashrc影响当前用户使用的bash shell)
	配置文件末尾添加：export CUDA_VISIBLE_DEVICES=0,1 
	##仅显卡设备0,1GPU可见。可用的GPU可通过nvidia-smi -L命令查看
	source使配置生效。

***ubuntu系统spyder无法中文注释
	找到文件 
	/usr/lib/x86_64-linux-gnu/qt5/plugins/platforminputcontexts/libfcitxplatforminputcontextplugin.so
	复制到
	/home/wangzhongju/anaconda3/envs/tensorflow/plugins/platforminputcontexts
	其中用户名和安装spyder环境对应更改即可。

***Jupyter qtconsole初始化配置
	1.首先生成jupyter qtconsoel的配置文件
		$ jupyter qtconsole --generate-config
	2.修改配置文件jupyter_qtconsole_config.py
		修改其中的height和width，c.ConsoleWidget.font_size = 14代表字体大小

***关于jupyter-notebook
	python -m pip install jupyter（当前python环境中安装）


***matplotlib.pyplot画子图的三种方法
   import numpy as np
   from matplotlib import pyplot as plt
   
   x = np.linspace(1, 100, num= 25, endpoint = True)
   style_list = ["g+-", "r*-", "b.-", "yo-"]
   def y_subplot(x,i):
       return np.cos(i*np.pi*x)
   1.plt.subplots
	f, ax = plt.subplots(2,2)
	ax[0][0].plot(x,y,color)
	ax[0][1].plot(x,y,color)
	...
	使用subplots会返回两个结果，一个是matplotlib.figure.Figure，也就是f，另一个是Axes object or array
	of Axes objects，也就是ax；f理解为大图，ax理解为包含很多小图对象的array。
   2.plt.subplot
	for i in range(1,5):
	    plt.subplot(2,2,i)
	    plt.plot(x,y_subplot(x,i), style_list[i-1])
	plt.show()
   3.plt.add_subplots
	fig = plt.figure
	for i in range(1,5):
	    ax = fig.add_subplot(2,2,i)
	    ax.plot(x,y_subplot(x,i),style_list[i-1])

***GPU 版 TensorFlow failed to create cublas handle: CUBLAS_STATUS_ALLOC_FAILED
	初始化Session时为其分配固定数量的显存
		gpu_options = tf.GPUOptions(per_process_gpu_memory_fraction=0.333)
		sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))

































