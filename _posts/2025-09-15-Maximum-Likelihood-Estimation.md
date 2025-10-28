---
title: "Maximun Likelihood Estimation"
date: 2025-09-15 12:00:00 +0800
categories: [AI, Math] # [Top_category, sub_category]
tags: [MLE]      # TAG names should always be lowercase
# author:gaoshan
---

# 极大似然估计 (MLE)：从原理到 AI 应用全景

## 1. 引言

在人工智能与机器学习的学习过程中，经常会听到“极大似然估计（Maximum Likelihood Estimation, MLE）”。它几乎无处不在：从最简单的逻辑回归，到深度学习里的交叉熵损失，再到语言模型 GPT 的训练目标，MLE 都是核心思想。

很多人初次接触时觉得抽象：**“似然”和“概率”到底有啥区别？为什么求导就能得到最优解？MLE 在 AI 里具体用在哪里？**
本文将从通俗直观的例子出发，逐步深入，再展示 MLE 在 AI 里的全景应用。


---

## 2. 极大似然估计是什么？

一句话理解：

> **极大似然估计就是利用已知的样本数据，反推出最可能导致这些数据出现的模型参数。**

换句话说：

* **模型已定**：假设数据来自某个分布（如正态分布）。
* **参数未知**：需要根据观察到的样本来估计模型参数。
* **目标**：选择参数，使得“观测数据出现的可能性”最大。

数学定义：
设样本 $x_1, x_2, \dots, x_n$ 来自分布 $p(x|\theta)$，其中 $\theta$ 是参数。

* 似然函数：

$$
L(\theta) = \prod_{i=1}^n p(x_i|\theta)
$$

* 对数似然函数：

$$
\ell(\theta) = \sum_{i=1}^n \ln p(x_i|\theta)
$$

* 最大似然估计：

$$
\hat{\theta} = \arg\max_\theta \ell(\theta)
$$

这里需要特别注意：我们要求解的是 **参数 $\theta$**，而不是直接“找 $\theta$ 的最大值”。真正要最大化的是 **似然函数 $L(\theta)$**。

---

## 3. 直观例子：黑白球罐子问题

假设有一个罐子，里面装着黑白球，比例未知。我们希望估计白球的比例 $p$。

* 从罐子里有放回地抽球 100 次，结果 70 次白球，30 次黑球。
* 似然函数为：

$$
L(p) = p^{70}(1-p)^{30}
$$

* 对数似然为：

$$
\ell(p) = 70\ln p + 30 \ln(1-p)
$$

* 求导：

$$
\frac{d\ell}{dp} = \frac{70}{p} - \frac{30}{1-p} = 0
$$

解得：

$$
\hat{p} = 0.7
$$

这就是最大似然估计：它选择了让“观察到 70 白 + 30 黑”这种结果概率最大的参数值。

---

## 4. 为什么“导数=0”就能找到最大值？

* **导数=0** 是极值点，但可能是极大、极小或拐点。
* 所以需要进一步判断：

  * 二阶导数 $\ell''(p) < 0$ → 最大值。
  * 二阶导数 $\ell''(p) > 0$ → 最小值。
* 在实际 MLE 问题里，很多似然函数是单峰的（unimodal），所以只要解出来的点通常就是最大值。

在球罐子例子中：

$$
\ell''(p) = -\frac{70}{p^2} - \frac{30}{(1-p)^2} < 0
$$

因此解确实是最大值。

---

## 5. 与正态分布的联系

假设数据来自正态分布 $N(\mu, \sigma^2)$：

* MLE 解出：

  * 均值 $\mu$ 的估计值是样本均值 $\hat{\mu} = \frac{1}{n}\sum x_i$。
  * 方差 $\sigma^2$ 的估计值是样本方差。

这说明 MLE 是我们熟悉的 **“用样本均值估计总体均值”** 的理论基础。
换句话说：MSE（均方误差）、交叉熵损失等常见损失函数，本质上都来源于 MLE。

---

## 6. MLE 在 AI 的典型应用

### (1) 概率建模

* **高斯混合模型 (GMM)**：通过 MLE + EM 算法估计均值、协方差和混合系数。
* **隐马尔可夫模型 (HMM)**：参数学习就是在最大化似然。

### (2) 判别模型

* **逻辑回归 (Logistic Regression)**：MLE 对应负对数似然损失。
* **Softmax 分类器**：深度学习里的交叉熵损失就是 MLE 的对数形式。

### (3) 深度学习

* **监督学习**：

  * 交叉熵损失 ⇔ 分类假设多项式分布 + MLE。
  * MSE ⇔ 假设高斯分布 + MLE。
* **语言模型 (GPT, BERT)**：训练目标是最大化句子似然。
* **变分自编码器 (VAE)**：目标是最大化似然下界（ELBO）。

### (4) 强化学习

* **策略梯度方法 (Policy Gradient)**：优化动作 log-likelihood，就是最大化似然。

---

## 7. 代表性论文

* **Logistic Regression**
  Cox, D. R. (1958). *The regression analysis of binary sequences.*

* **EM 算法**
  Dempster, A. P., Laird, N. M., & Rubin, D. B. (1977). *Maximum likelihood from incomplete data via the EM algorithm.*

* **HMM 在语音识别**
  Rabiner, L. (1989). *A tutorial on hidden Markov models and selected applications in speech recognition.*

* **深度学习教材**
  Goodfellow, I., Bengio, Y., & Courville, A. (2016). *Deep Learning.*

* **VAE**
  Kingma, D. P., & Welling, M. (2014). *Auto-encoding variational Bayes.*

* **神经语言模型**
  Bengio, Y., Ducharme, R., Vincent, P., & Jauvin, C. (2003). *A neural probabilistic language model.*

---

## 8. 总结

极大似然估计不仅是统计学的基本思想，更是现代 AI 的核心基石。

* 它让我们能够从样本中“反推”最合理的模型参数；
* 它解释了为什么均值、方差、交叉熵、MSE等常用指标合理；
* 它几乎出现在所有 AI 任务中，从经典的 GMM/HMM 到最新的大模型 GPT。

👉 换句话说：
**只要你在最小化交叉熵或均方误差，本质上你就在做极大似然估计。**

---

