---
title: "机器学习笔记"
date:
tags:
    - 算法
---

# 机器学习

## 目录



## 线性回归

[返回目录](#目录)

### 一元线性回归

#### 算法思想
1. 找到一条曲线 **y = wx + b** ，使得能够根据自变量x 尽可能准确地预测因变量y
2. 模型最优解为
\[ (w^*, b^*) = \arg\min_{i=0}^m (y_i - \hat{y}_i)^2 \]
即调整w和b使得预测误差平方和最小

#### 评价指标
1. 均方误差：误差越小，模型预测效果越好
\[ MSE = \frac{1}{m} \sum_{i=1}^m (y_i - \hat{y}_i)^2  \]
2. 均方根误差：误差越小，模型预测效果越好
\[ RMSE = \sqrt{MSE} = \sqrt{\frac{1}{m} \sum_{i=1}^m (y_i - \hat{y}_i)^2} \]
3. 回归平方和：衡量​​回归模型“解释”的因变量变异程度​​（即模型通过自变量预测后，预测值与均值的差异）
\[ SSR = \sum_{i=1}^m (\hat{y_i} - \bar{y})^2  \]
4. 残差平方和：衡量​​回归模型“未解释”的因变量变异程度​​（即预测值与实际值的误差）
\[ SSE = \sum_{i=1}^m (y_i - \hat{y}_i)^2  \]
5. 总平方和：衡量​​因变量 y自身的总变异程度​​（即数据自然波动的大小）
\[ SST = \sum_{i=1}^m (y_i - \bar{y})^2 = SSE + SSR \]
6. 决定系数R^2^: 衡量​​回归模型对因变量变异的解释比例​​（取值范围 [0,1]）
\[ R^2 = \frac{SSR}{SST} = 1 - \frac{SSE}{SST}  \]
实际中，R2越接近1，说明模型解释的变异越多，拟合效果越好
<br>
1. 调整后的决定系数R^2^~d~：在 R2基础上，​​惩罚“无意义的自变量加入”​​，更适合比较不同复杂度的模型
\[ R^2_d = 1 - \frac{m-1}{m-d-1} \cdot \frac{SSE}{SST}  \]
其中，m为样本量，d为自变量个数，本质是对自由度的惩罚

#### python中的实现
1. 引入pandas模块，读取数据

~~~python
import pandas as pd
dataframe = pd.read_csv('data.csv')
~~~

| 方法 | 描述 | 参数说明 | 返回值 |
| --- | --- | --- | --- |
|`pandas.read_csv(filename)` | 读取csv文件 | 文件名或文件路径 | DataFrame |
|`dataframe.iloc[行索引器，列索引器]` | 获取行和列 | 行索引器(切片)：表示为 `start:stop` 即行的选择范围，当都为空时，默认选择所有行 <br> 列索引器(切片)：表示为 `start:stop` 即列的选择范围，当都为空时，默认选择所有列 | DataFrame |

2. 引入matplotlib模块，绘制图像，其中散点图为pyplot模块中的scatter()方法

~~~python
import matplotlib.pyplot as plt
# 绘制散点图
plt.scatter(dataframe['x'], dataframe['y'], color='red')
# 显示图像
plt.show()
~~~

| 方法 | 描述 | 参数说明 | 返回值 |
| --- | --- | --- | --- |
|`matplotlib.pyplot.scatter(x, y, `<br> `s=None, c=None, marker=None,`<br> `cmap=None, norm=None, vmin=None, vmax=None, alpha=None, linewidths=None, verts=None, edgecolors=None, *, plotnonfinite=False, data=None, **kwargs)` | 绘制数据点 | x: 数据点的横坐标列表<br> y: 数据点的纵坐标列表<br> s: 数据点的大小列表<br> c: 数据点的颜色列表<br> marker: 数据点的标记列表<br> cmap: 颜色映射对象<br> norm: 颜色映射对象<br> vmin: 颜色映射对象<br> vmax: 颜色映射对象<br> alpha: 数据点的透明度列表<br> linewidths: 数据点的线宽列表<br> verts: 数据点的标记列表<br> edgecolors: 数据点的边框颜色列表<br> plotnon | 无 |
|`matplotlib.pyplot.show()`|显示图|无|无|
|`matplotlib.pyplot.figure()`|创建一个新图|无|无|

3. 引入statsmodels模块，增加截距项，进行数据预处理

~~~python
import statsmodels.api as sm
# 增加截距项，即在原数据最右侧新增一列全为1的列'const'
df_const = sm.add_constant(dataframe)
# 将数据集X和y分别提取出来
X = df_const.iloc[:, :-1] # 选取除了截距项的所有列作为自变量矩阵
y = df_const['y'] # 提取因变量列作为因变量向量
~~~

|方法|描述|参数说明|返回值|
|---|---|---|---|
|`statsmodels.api.add_constant(dataframe)`|增加截距项，即在原数据最右侧新增一列全为1的列'const'|dataframe|增加截距项后的数据集dataframe|
|`statsmodels.api.OLS(y, X)`|建立OLS模型|y: 因变量向量<br>X: 自变量矩阵|OLS模型对象|
|`statsmodels.api.qqplot(data, `<br>`dist=<scipy.stats._continuous_distns.norm_gen object>, <br>line='45', `<br>`ax=None, <br>**kwargs)`|绘制QQ图|data（必选参数）:待检验的样本数据（一维数据）<br> dist（可选参数）:检验分布，默认为正态分布 <br>line（可选参数）: 绘制的参考线，默认为45度线 's'：绘制标准化分位数的参考线（适用于非标准分布）'r--'：自定义直线样式（如红色虚线）<br> ax（可选参数）: 绘图对象 <br>kwargs（可选参数）:绘图参数|无|
|`statsmodels.api.graphics.plot_regress_exog(model, `<br>`exog_idx, `<br>`fig=None, `<br>`ax=None, `<br>`**kwargs)`|绘制自变量和因变量的回归图|model: OLS模型对象<br>exog_idx: 自变量索引<br>fig（可选参数）: 绘图对象<br>ax（可选参数）: 绘图对象<br>kwargs（可选参数）:绘图参数|无|
|`model.fit()`|训练模型|无|无|
|`model.predict(X)`|预测|X: 自变量矩阵|预测结果向量|
|`model.summary()`|查看模型报告|无|无|

4. 建立OLS模型，并训练模型，得到训练结果

~~~python
# 建立OLS模型，并训练模型
model = sm.OLS(y, X).fit()
# 预测
y_pred = model.predict(X)
# 查看模型报告
model.summary()
~~~
OLS即普通最小二乘法，即最小化残差平方和
实现步骤：
* RSS
\[ \text{RSS}(\hat{\beta}_0, \hat{\beta}_1) = \sum_{i=1}^n e_i^2 = \sum_{i=1}^n \left( y_i - \hat{\beta}_0 - \hat{\beta}_1 x_i \right)^2 \]
* 对所有的参数β求偏导
\[ \frac{\partial \text{RSS}}{\partial \hat{\beta}_0} = -2 \sum_{i=1}^n \left( y_i - \hat{\beta}_0 - \hat{\beta}_1 x_i \right) \]
\[ \frac{\partial \text{RSS}}{\partial \hat{\beta}_1} = -2 \sum_{i=1}^n x_i \left( y_i - \hat{\beta}_0 - \hat{\beta}_1 x_i \right) \]
* 令导数等于0，联立公式求解，得到所有的参数β
\[ \begin{cases} 
\sum_{i=1}^n \left( y_i - \hat{\beta}_0 - \hat{\beta}_1 x_i \right) = 0 \\
\sum_{i=1}^n x_i \left( y_i - \hat{\beta}_0 - \hat{\beta}_1 x_i \right) = 0 
\end{cases} \]
* 解方程组，得到参数β
\[ \hat{\beta}_1 = \frac{\sum_{i=1}^n (x_i - \bar{x})(y_i - \bar{y})}{\sum_{i=1}^n (x_i - \bar{x})^2} （斜率） \]
\[ \hat{\beta}_0 = \bar{y} - \hat{\beta}_1 \bar{x} （截距）\]

5. 绘制QQ图
~~~python
# 绘制QQ图，用于检验残差是否服从正态分布
sm.qqplot(model.resid, line='r')
plt.show()
~~~
* 理想情况（完全拟合）

数据点​​严格沿参考线（如45°线）分布​​，说明样本分位数与理论分位数几乎一致，数据完全服从理论分布。
* 轻微偏离（可接受）

数据点整体沿参考线分布，但存在​​局部小幅偏离​​（如在尾部或中间位置有少量点偏离），可能是随机误差导致，通常认为数据近似服从理论分布。
* 显著偏离（不拟合）

数据点明显偏离参考线，呈现以下模式时，提示数据与理论分布存在显著差异：

    * 尾部偏离​​：数据点在两端（低分位数或高分位数）偏离参考线（如右偏数据的右尾上翘），说明数据存在厚尾或偏态；
    * ​整体偏移​​：数据点整体平行于参考线但不重合（如整体高于或低于参考线），说明数据分布的位置参数（如均值）与理论分布不同；
    * ​曲线偏离​​：数据点呈曲线形态偏离参考线，说明数据分布的形状参数（如方差、峰度）与理论分布不同。

6. 残差分析
~~~python
# 残差分析
fig = plt.figure(figsize=(12, 8))
# 绘制残差图
fig = sm.graphics.plot_regress_exog(model, 'x', fig=fig)
~~~