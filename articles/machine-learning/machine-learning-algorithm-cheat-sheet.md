<properties
	pageTitle="기계 학습 알고리즘 치트 시트 | Microsoft Azure"
	description="인쇄 가능한 기계 학습 알고리즘 치트 시트를 사용하면 Azure 기계 학습 스튜디오에서 예측 모델에 적합한 알고리즘을 선택합니다."
	keywords="알고리즘 치트 시트, 치트 시트, 기계 학습 알고리즘"
	services="machine-learning"
	documentationCenter=""
	authors="brohrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="brohrer;garye" />


# Microsoft Azure 기계 학습 스튜디오용 기계 학습 알고리즘 치트 시트

**Microsoft Azure 기계 학습 알고리즘 치트 시트**를 사용하면 예측 분석 모델에 대한 적합한 알고리즘을 선택할 수 있습니다.

[Azure 기계 학습 스튜디오](https://studio.azureml.net/)에는 ***회귀***, ***분류***, ***클러스터링***, ***이상 감지*** 등의 대규모 알고리즘 라이브러리가 있습니다. 각 항목은 다양한 유형의 기계 학습 문제를 해결하기 위해 고안되었습니다.


## 다운로드: 기계 학습 알고리즘 치트 시트

**여기서 치트 시트 다운로드: [기계 학습 알고리즘 치트 시트(11x17인치)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![기계 학습 알고리즘 치트 시트: 기계 학습 알고리즘을 선택하는 방법에 대해 알아봅니다.][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

간편하게 보관하면서 알고리즘을 선택할 수 있도록 tabloid 크기로 기계 학습 알고리즘 치트 시트를 다운로드하여 인쇄합니다.

> [AZURE.NOTE] 이 치트 시트를 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 기계 학습을 위한 알고리즘 선택 방법](machine-learning-algorithm-choice.md) 문서를 참조하세요.

## 알고리즘에 대한 자세한 도움말

* 다양한 유형의 기계 학습 알고리즘, 사용 방법 및 적합한 알고리즘을 선택하기 위해 이 치트 시트 사용 방법에 대한 자세한 논의는 [Microsoft Azure 기계 학습에서 알고리즘을 선택하는 방법](machine-learning-algorithm-choice.md)을 참조하세요.
* 기계 학습 스튜디오에서 사용할 수 있는 모든 기계 학습 알고리즘의 범주별 목록은 기계 학습 스튜디오 Algorithm 및 모듈 도움말에서 [초기화 모델][initialize-model]을 참조하세요.
* 기계 학습 스튜디오의 전체 알고리즘 및 모듈 목록은 기계 학습 스튜디오 알고리즘 및 모듈 도움말에서 [기계 학습 스튜디오 모듈의 A-Z 목록][a-z-list]을 참조하세요.
* 기계 학습 스튜디오의 기능을 개략적으로 제공하는 다이어그램을 다운로드하고 인쇄하려면 [Azure 기계 학습 스튜디오 기능 개요 다이어그램](machine-learning-studio-overview-diagram.md)을 참조하세요.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- This needs to be updated based on the new Choosing and Algorithm article

## Notes and terminology definitions for the machine learning algorithm cheat sheet

* The suggestions offered in this algorithm cheat sheet are approximate rules-of-thumb. Some can be bent, and some can be flagrantly violated. This is intended to suggest a starting point. Don’t be afraid run a head-to-head competition between several algorithms on your data. There is simply no substitute for understanding the principles of each algorithm and understanding the system that generated your data.

* Every machine learning algorithm has its own style or *inductive bias*. For a specific problem, several algorithms may be appropriate and one algorithm may be a better fit than others. But knowing which will be the best fit beforehand is not always possible. In cases like these, several algorithms are listed together in the cheat sheet. An appropriate strategy would be to try one algorithm, and if the results are not yet satisfactory, try the others. Here’s an example from the [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) of an experiment that tries several algorithms against the same data and compares the results: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* There are three main categories of machine learning: **supervised learning**, **unsupervised learning**, and **reinforcement learning**.

  * In **supervised learning**, each data point is labeled or associated with a category or value of interest.  An example of a categorical label is assigning an image as either a ‘cat’ or a ‘dog’.  An example of a value label is the sale price associated with a used car. The goal of supervised learning is to study many labeled examples like these, and then to be able to make predictions about future data points - for example, to identify new photos with the correct animal or to assign accurate sale prices to other used cars. This is a popular and useful type of machine learning. All of the modules in Azure Machine Learning are supervised learning algorithms except for [K-Means Clustering][k-means-clustering].

  * In **unsupervised learning**, data points have no labels associated with them. Instead, the goal of an unsupervised learning algorithm is to organize the data in some way or to describe its structure. This can mean grouping it into clusters, as K-means does, or finding different ways of looking at complex data so that it appears simpler.

  * In **reinforcement learning**, the algorithm gets to choose an action in response to each data point. It is a common approach in robotics, where the set of sensor readings at one point in time is a data point, and the algorithm must choose the robot’s next action. It's also a natural fit for Internet of Things applications. The learning algorithm also receives a reward signal a short time later, indicating how good the decision was. Based on this, the algorithm modifies its strategy in order to achieve the highest reward. Currently there are no reinforcement learning algorithm modules in Azure ML.

* **Bayesian methods** make the assumption of statistically independent data points. This means that the unmodeled variability in one data point is uncorrelated with others, that is, it can’t be predicted. For example, if the data being recorded is the number of minutes until the next subway train arrives, two measurements taken a day apart are statistically independent. However, two measurements taken a minute apart are not statistically independent - the value of one is highly predictive of the value of the other.

* **Boosted decision tree regression** takes advantage of feature overlap or interaction among features. That means that, in any given data point, the value of one feature is somewhat predictive of the value of another. For example, in daily high/low temperature data, knowing the low temperature for the day allows you to make a reasonable guess at the high. The information contained in the two features is somewhat redundant.

* Classifying data into more than two categories can be done by either using an inherently multi-class classifier, or by combining a set of two-class classifiers into an **ensemble**. In the ensemble approach, there is a separate two-class classifier for each class - each one separates the data into two categories:  “this class” and “not this class.” Then these classifiers vote on the correct assignment of the data point. This is the operational principle behind [One-vs-All Multiclass][one-vs-all-multiclass].

* Several methods, including logistic regression and the Bayes point machine, assume **linear class boundaries**, that is, that the boundaries between classes are approximately straight lines (or hyperplanes in the more general case). Often this is a characteristic of the data that you don’t know until after you’ve tried to separate it, but it’s something that typically can be learned by visualizing beforehand. If the class boundaries look very irregular, stick with decision trees, decision jungles, support vector machines, or neural networks.

* Neural networks can be used with categorical variables by creating a **dummy variable** for each category and setting it to 1 in cases where the category applies, 0 where it doesn’t.

-->

<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/

<!---HONumber=AcomDC_0914_2016-->