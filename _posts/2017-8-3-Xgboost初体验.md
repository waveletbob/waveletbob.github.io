---
layout: post
title: Xgboost初体验
categories: 数据科学
tags: Xgboost

---

### 安装过程 ###

- 准备
	- git
	- Mingw
		
- 编译

		git clone --recursive https://github.com/dmlc/xgboost
		cd xgboost
		git submodule init
		git submodule update
		alias make='mingw32-make'
		cd dmlc-core
		make -j4
		cd ../rabit
		make lib/librabit_empty.a -j4
		cd ..
		cp make/mingw64.mk config.mk
		make -j4

	
- 安装
	- cd /Xgboost/python-package
	- python setup.py install

### 1）What？ ###

XGBoost ：eXtreme Gradient Boosting

项目地址：https://github.com/dmlc/xgboost

在很多Kaggle的比赛中，很多喜欢用Xgboost的，而且获得了很好的成绩和表现。它是由 Tianqi Chen http://homes.cs.washington.edu/~tqchen/ 最初开发的实现可扩展，便携，分布式 gradient boosting (GBDT, GBRT or GBM) 算法的一个库，可以下载安装并应用于 C++，Python，R，Julia，Java，Scala，Hadoop，现在有很多协作者共同开发维护。

XGBoost 所应用的算法就是 gradient boosting decision tree，既可以用于分类也可以用于回归问题中。

**什么是 Gradient Boosting？**

boosting 其中的一种方法，将弱分离器 f_i(x) 组合起来形成强分类器 F(x) 的一种方法。有三个要素：

- A loss function to be optimized：
例如分类问题中用 cross entropy，回归问题用 mean squared error。
- A weak learner to make predictions：
例如决策树。
- An additive model：
将多个弱学习器累加起来组成强学习器，进而使目标损失函数达到极小。

通俗来讲就是不断添加新的学习器减小误差达到组合更优的模型，只不过在优化过程中使用了梯度下降的优化算法来最小化损失函数。

基于GB的实现有两种：AdaBoost和XGBoost

XGBoost的优点是相比GB计算速度快，模型表现好

原因：

- Parallelization
- Distributed Computing
- Out-of-Core Computing
- Cache Optimization of data structures and algorithms

![](http://mmbiz.qpic.cn/mmbiz_png/BnSNEaficFAbO6ECrUDM1aROW046Yh3Rg2dRKQ9WrWicfxrRkpRKFK2yibLGZ4QQOs7CpFVWiavS1ft0fwSqRcZJAg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 2）应用 ###

**数据集**：[https://archive.ics.uci.edu/ml/machine-learning-databases/pima-indians-diabetes/pima-indians-diabetes.data](https://archive.ics.uci.edu/ml/machine-learning-databases/pima-indians-diabetes/pima-indians-diabetes.data)

**教程**：[https://xgboost.readthedocs.io/en/latest/](https://xgboost.readthedocs.io/en/latest/)


实例代码：

	#coding:utf-8
	
	from numpy import loadtxt
	from xgboost import XGBClassifier
	from sklearn.model_selection import train_test_split
	from sklearn.metrics import accuracy_score
	
	dataset = loadtxt('pima-indians-diabetes.csv', delimiter=",")
	X = dataset[:,0:8]
	Y = dataset[:,8]
	
	seed = 7
	test_size = 0.33
	X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size=test_size, random_state=seed)
	
	model = XGBClassifier()
	#调参
	from sklearn.model_selection import GridSearchCV
	from sklearn.model_selection import StratifiedKFold
	learning_rate = [0.0001, 0.001, 0.01, 0.1, 0.2, 0.3]
	param_grid = dict(learning_rate=learning_rate)
	kfold = StratifiedKFold(n_splits=10, shuffle=True, random_state=7)
	grid_search = GridSearchCV(model, param_grid, scoring="neg_log_loss", n_jobs=-1, cv=kfold)
	grid_result = grid_search.fit(X, Y)
	print("Best: %f using %s" % (grid_result.best_score_, grid_result.best_params_))
	means = grid_result.cv_results_['mean_test_score']
	stds = grid_result.cv_results_['std_test_score']
	params = grid_result.cv_results_['params']
	for mean, stdev, param in zip(means, stds, params):
	    print("%f (%f) with: %r" % (mean, stdev, param))
	
	
	#监控模型
	# eval_set = [(X_test, y_test)]
	# model.fit(X_train, y_train,early_stopping_rounds=10, eval_metric="logloss", eval_set=eval_set, verbose=True)
	
	#画出特征重要度
	# from xgboost import plot_importance
	# from matplotlib import pyplot
	# model.fit(X_train, y_train)
	# plot_importance(model)
	# pyplot.show()
	
	y_pred = model.predict(X_test)
	predictions = [round(value) for value in y_pred]
	accuracy = accuracy_score(y_test, predictions)
	print("Accuracy: %.2f%%" % (accuracy * 100.0))

