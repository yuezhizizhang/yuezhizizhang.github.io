---
layout: post
title:  "Tensorflow with Anaconda and Jupyter Notebook on Windows"
categories: "Tensorflow Anaconda Jupyter"
---

Machine learning is really an enchanting subject. I'm starting with it, aiming at training out a stock market predictor, LOL.

About Machine Learning, the best tutorials I have found out so far is the Standford open class:

* [Stanford Open Class Machine Learning](http://openclassroom.stanford.edu/MainFolder/CoursePage.php?course=MachineLearning)

You are able to watch the whole series of class videos on Youtube. I have downloaded the videos from Youtube via tool [youtube-dl](https://github.com/rg3/youtube-dl).

To getting start with Tensorflow, you can read these two tutorials:

* [Gentlest Introduction to Tensorflow](https://medium.com/all-of-us-are-belong-to-machines/the-gentlest-introduction-to-tensorflow-248dc871a224)

* [Learning Tensorflow](http://learningtensorflow.com/index.html)

To install Tensorflow with Anaconda on Windows, you have to follow the [Steps on Tensorflow website](https://www.tensorflow.org/install/install_windows#installing_with_anaconda). To run the tensorflow environment, use command:

```cmd
C:\Users\fiona>activate tensorflow
```

After that, you have to install Jupyter Notebook into your tensorflow conda environment with command:

```cmd
(tensorflow) C:\Users\fiona>conda install jupyter
```

To check whether Jupyter is correctly installed under the tensorflow environment, use command:

```cmd
(tensorflow) C:\Users\fiona>which jupyter
```

The correct path should be like this:

> /c/Users/fiona/AppData/Local/conda/conda/envs/tensorflow/Scripts/jupyter

And make sure Jupyter is pointing to the correct Python:

```cmd
(tensorflow) C:\Users\fiona>jupyter kernelspec list
```

For the tensorflow environment, the correct python path should be like this:

```cmd
Available kernels:
  python3    C:\Users\fiona\AppData\Local\conda\conda\envs\tensorflow\lib\site-packages\ipykernel\resources
```

If you launch Jupyter Notebook from path **C:\\**

```cmd
(tensorflow) C:\>jupyter notebook
```

you will meet error:

> 404 : Not Found. You are requesting a page that does not exist!

On the command console, it says:

> Refusing to serve hidden directory via 404 Error.

To work around this error, you can either run your Cmd as Administrator, or you should go to user's home directory to launch Jupyter Notebook.

```cmd
(tensorflow) C:\>cd c:\Users\fiona
(tensorflow) C:\Users\fiona>jupyter notebook
```

Done. It paves the way for your adventure with Tensorflow and Machine Learning.