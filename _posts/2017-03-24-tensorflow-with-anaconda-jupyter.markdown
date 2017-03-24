---
layout: post
title:  "Tensorflow with Anaconda and Jupyter Notebook on Windows"
categories: "Tensorflow Anaconda Jupyter"
---

Machine learning is really an enchanting subject. I'm starting with it, aiming at training out a stock market predictor, LOL.

About Machine Learning, the best tutorials I have found out so far is the Standford open class:

* [Stanford Open Class Machine Learning](http://openclassroom.stanford.edu/MainFolder/CoursePage.php?course=MachineLearning)

You are able to watch the whole series of class videos on Youtube. I have downloaded the videos from Youtube with tool [youtube-dl](https://github.com/rg3/youtube-dl).

To getting start with Tensorflow, you can refer to these two tutorials:

* [Gentlest Introduction to Tensorflow](https://medium.com/all-of-us-are-belong-to-machines/the-gentlest-introduction-to-tensorflow-248dc871a224)

* [Learning Tensorflow](http://learningtensorflow.com/index.html)

To install Tensorflow with Anaconda on Windows, you have to follow the [Steps on Tensorflow website](https://www.tensorflow.org/install/install_windows#installing_with_anaconda). After that, you have to install Jupyter into your tensorflow conda environment with command:

```cmd
(tensorflow) C:\>pip install jupyter
```

However, when you run jupyter notebook after installation, you will meet error:

> 404 : Not Found. You are requesting a page that does not exist!

On the command console, it says:

> Refusing to serve hidden directory via 404 Error.

The easist way to work around this permission issue is to *Run your Cmd as Administrator*.

```cmd
C:\WINDOWS\system32>activate tensorflow

(tensorflow) C:\WINDOWS\system32>jupyter notebook
```

Done. You can start your adventure with Tensorflow and Machine Learning now.