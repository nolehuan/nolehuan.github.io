---
title: interview
date: 2023-12-31 15:55:29
abstracts: interview review
tags:
  - CNN
categories:
  - learning
cover: ''
feature: false
mathjax: false
---


## 面经

[https://leetcode-cn.com/circle/discuss/7LCBce/](https://leetcode-cn.com/circle/discuss/7LCBce/)

### 插值方法

**最近邻插值法**

**双线性内插法**

$f\left(x, y_{1}\right)=f\left(x_{1}, y_{1}\right) \cdot \frac{x_{2}-x}{x_{2}-x_{1}}+f\left(x_{2}, y_{1}\right) \cdot \frac{x-x_{1}}{x_{2}-x_{1}}$

$f\left(x, y_{2}\right)=f\left(x_{1}, y_{2}\right) \cdot \frac{x_{2}-x}{x_{2}-x_{1}}+f\left(x_{2}, y_{2}\right) \cdot \frac{x-x_{1}}{x_{2}-x_{1}}$

$f(x, y)=f\left(x, y_{1}\right) \cdot \frac{y_{2}-y}{y_{2}-y_{1}}+f\left(x, y_{2}\right) \cdot \frac{y-y_{1}}{y_{2}-y_{1}}$

**三次内插法、立方卷积插值**

考虑周围 16 个像素值

$W(x)= \begin{cases}(a+2)|x|^{3}-(a+3)|x|^{2}+1 & \text { for }|x| \leq 1 \\ a|x|^{3}-5 a|x|^{2}+8 a|x|-4 a & \text { for } 1<|x|<2 \\ 0 & \text { otherwise }\end{cases}$

$a=-0.5$

分别计算 $x$ 轴和 $y$ 轴的 BiCubic 函数值，然后相乘得到最终的权值，再加权相加

$f(x,y)=\sum_{i=0}^3\sum_{j=0}^3f(x_i,y_j)W(x-x_i)W(y-y_i)$

### 天平找次品

$n$ 个货物中有一个较轻的次品，至少用天平称多少次一定可以找到次品

三等分一次排除三分之二

$n\%3=0$

$n\%3=1$  $n\%3=2$ 称数量相同的两组

- code
    
    ```cpp
    int find_defective(int n)
    {
    	int ans = 0;
    	if(n % 3 == 0) { n--; } // 9 / 3 = 3, 3 / 3 = 1
    	while(n > 0) {
    		n /= 3;
    		ans++;
    	}
    	return ans;
    }
    ```
    

### 不规则多边形面积

通过每条线段下方的面积进行计算

### 判断点是否在多边形内

射线法，从点向某个方向作射线，根据与多边形交点个数的奇偶判断

### KNN

- code
    
    ```python
    import numpy as np
    
    def createData():
        group = np.array([[1.0, 2.0], [1.2, 0.1], [0.1, 1.4], [0.3, 3.5]])
        labels = ['A', 'A', 'B', 'B']
        return group, labels
    
    def classify(input, data, label, k):
        dataSize = data.shape[0]
        assert(k <= dataSize)
        diff = np.tile(input, (dataSize, 1)) - data
        sqdiff = diff ** 2
        squareDist = np.sum(sqdiff, axis = 1)
        dist = squareDist ** 0.5
        sortedDistIndex = np.argsort(dist)
    
        classCount = {}
        for i in range(k):
            voteLabel = label[sortedDistIndex[i]]
            classCount[voteLabel] = classCount.get(voteLabel, 0) + 1
        maxCount = 0
        for key, value in classCount.items():
            if value > maxCount:
                maxCount = value
                classes = key
    
        return classes
    
    if __name__ == "__main__":
        input = np.array([1.1, 0.9])
        data, label = createData()
        classes = classify(input, data, label, 2)
        print(classes)
    ```
    

### Kmeans

- code
    
    ```python
    import numpy as np
    from math import sqrt
    
    def distElud(vecA, vecB):
        return sqrt(sum(np.power((vecA - vecB), 2)))
    
    def randCent(data, k):
        n = np.shape(data)[1]
        center = np.mat(np.zeros((k, n)))
        for j in range(n):
            rangeJ = float(max(data[:, j]) - min(data[:, j]))
            center[:, j] = min(data[:, j]) + rangeJ * np.random.rand(k, 1)
        return center
    
    def kmeans(data, k, dist = distElud, createCent = randCent):
        m = np.shape(data)[0]
        clusterAssment = np.mat(np.zeros((m, 2)))
        center = createCent(data, k)
        # print(center)
        clusterChanged = True
        while clusterChanged:
            clusterChanged = False
            for i in range(m):
                minDist = np.inf
                minIndex = -1
                for j in range(k):
                    distJI = dist(data[i, :], np.array(center[j, :]).squeeze(0))
                    if distJI < minDist:
                        minDist = distJI
                        minIndex = j
                if clusterAssment[i, 0] != minIndex:
                    clusterChanged = True
                clusterAssment[i,:] = minIndex, minDist ** 2
            for cent in range(k):
                # 将矩阵转化为 array 数组类型
                dataCent = data[np.nonzero(clusterAssment[:, 0].A == cent)[0]]
                # print(dataCent)
                if dataCent.shape[0] == 0:
                    continue
                center[cent, :] = np.mean(dataCent, axis = 0)
        return center, clusterAssment
    
    if __name__ == "__main__":
        data = np.array([[1, 2], [2, 1], [3, 1], [5, 4], [5, 5], [6, 5],
                        [10, 8], [7, 9], [11, 5], [14, 9], [14, 14]])
        center, clusterAssment = kmeans(data, 3)
        print(center)
        print(clusterAssment)
    ```
    

### DBSCAN

- code
    
    ```python
    # -*- coding: utf-8 -*-
    # https://github.com/choffstein/dbscan
    # A Density-Based Algorithm for Discovering Clusters in Large Spatial Databases with Noise
    # Martin Ester, Hans-Peter Kriegel, Jörg Sander, Xiaowei Xu
    # dbscan: density based spatial clustering of applications with noise
    
    import numpy as np
    import math
    
    UNCLASSIFIED = False
    NOISE = None
    
    def _dist(p, q):
    	return math.sqrt(np.power(p - q, 2).sum())
    
    def _eps_neighborhood(p, q, eps):
    	return _dist(p, q) < eps
    
    def _region_query(m, point_id, eps):
        n_points = m.shape[1]
        seeds = []
        for i in range(0, n_points):
            if _eps_neighborhood(m[:,point_id], m[:,i], eps):
                seeds.append(i)
        return seeds
    
    def _expand_cluster(m, classifications, point_id, cluster_id, eps, min_points):
        seeds = _region_query(m, point_id, eps)
        if len(seeds) < min_points:
            classifications[point_id] = NOISE
            return False
        else:
            classifications[point_id] = cluster_id
            for seed_id in seeds:
                classifications[seed_id] = cluster_id
            
            while len(seeds) > 0:
                current_point = seeds[0]
                results = _region_query(m, current_point, eps)
                if len(results) >= min_points:
                    for i in range(0, len(results)):
                        result_point = results[i]
                        if classifications[result_point] == UNCLASSIFIED or \
                            classifications[result_point] == NOISE:
                            if classifications[result_point] == UNCLASSIFIED:
                                seeds.append(result_point)
                            classifications[result_point] = cluster_id
                seeds = seeds[1:]
            return True
    
    def dbscan(m, eps, min_points):
        """
        Implementation of Density Based Spatial Clustering of Applications with Noise.
        See https://en.wikipedia.org/wiki/DBSCAN.
        scikit-learn probably has a better implementation.
        Uses Euclidean Distance as the measure.
        Inputs:
        m - A matrix whose columns are feature vectors.
        eps - Maximum distance two points can be to be regionally related.
        min_points - The minimum number of points to make a cluster.
        Outputs:
        An array with either a cluster id number or dbscan.NOISE (None) for each
        column vector in m.
        """
        cluster_id = 1
        n_points = m.shape[1]
        classifications = [UNCLASSIFIED] * n_points
        for point_id in range(0, n_points):
            point = m[:,point_id]
            if classifications[point_id] == UNCLASSIFIED:
                if _expand_cluster(m, classifications, point_id, cluster_id, eps, min_points):
                    cluster_id = cluster_id + 1
        return classifications
    
    def test_dbscan():
        m = np.matrix('1 1.2 0.8 3.7 3.9 3.6 10; 1.1 0.8 1 4 3.9 4.1 10')
        eps = 0.5
        min_points = 2
        assert dbscan(m, eps, min_points) == [1, 1, 1, 2, 2, 2, None]
    
    if __name__ == "__main__":
        test_dbscan()
    ```
    

### Conv + BN 加速策略

在 Inference 阶段，可以将 BN 层的参数合并在之前的 Linear 或 Conv 层中，加速推断，因为二者都是线性变换

$\mu=\frac{1}{m}\sum_{i=1}^mx_i$    $\sigma^2=\frac{1}{m}\sum_{i=1}^m(x_i-\mu)^2$

$\hat x_i=\frac{x_i-\mu}{\sqrt{\sigma^2+\epsilon}}$    $y_i=\gamma \cdot x_i+\beta$

$Y=\gamma\cdot(\frac{(W\cdot X+B)-\mu}{\sqrt{\sigma^2+\epsilon}})+\beta$

$W_{merged}=W\cdot\frac{\gamma}{\sqrt{\sigma^2+\epsilon}}$    $B_{merged}=(B-\mu)\cdot\frac{\gamma}{\sqrt{\sigma^2+\epsilon}}+\beta$

- code
    
    ```python
    module = nn.Conv2d(in_channels=8, out_channels=16, kernel_size=3, stride=1, padding=padding)
    bn_module = nn.BatchNorm2d(16, affine=True)
    
    w = module.weight.data    # shape[16, 8, 3, 3]
    b = module.bias.data      # shape[16] 可用全零代替
    ws = [1] * len(w.size())  # [1, 1, 1, 1]
    ws[0] = w.size()[0]       # [16, 1, 1, 1]
    
    invstd = bn_module.running_var.clone().add_(bn_module.eps).pow_(-0.5)
    w.mul_(invstd.view(*ws).expand_as(w))
    b.add_(-bn_module.running_mean).mul_(invstd)
    
    if bn_module.affine:
        w.mul_(bn_module.weight.data.view(*ws).expand_as(w))
        b.mul_(bn_module.weight.data).add_(bn_module.bias.data)
    ```
    

### Softmax    Softmax + CrossEntropy  反向求导

**Softmax**

$S(z_i)=\frac{e^{z_ i}}{\sum_{j=1}^{N} e^{z_{j}}}$

$\text{if}\ \ i == k:\\
\frac{\partial S_{i}}{\partial z_{k}}
=\frac{e^{z_{i}}}{\sum_{j=1}^{N} e^{z_{j}}}+\frac{-e^{z_{i}}}{\left(\sum_{j=1}^{N} e^{z_{j}}\right)^{2}}\\
=\frac{e^{z_i}}{\sum_{j=1}^{N} e^{z_{j}}} *\left(1-\frac{e^{z_i}}{\sum_{j=1}^{N} e^{z_{j}}}\right)\\
=S_{i}\left(1-S_{i}\right)$

$\text{if}\ \ i\ != k: \\
\frac{\partial S_{i}}{\partial z_{k}}=e^{z_{i}}*\frac{-e^{z_{k}}}{\left(\sum_{j=1}^{N} e^{z_j}\right)^{2}}\\
=-S_{i} * S_{k}$

**Softmax + CrossEntropy**

$L_{c e}=\sum_{i=1}^{N}-y_{i} * \log \left(S_{i}\right)$

$\begin{array}{l}
\frac{\partial L_{c e}}{\partial z_k}=\sum_{i=1}^{N}-y_{i} * \frac{\partial {\log\left(S_{i}\right)}}{{\partial z_{k}}} \\
=-y_{i} * \frac{1}{S_{i}} * S_{i} *\left(1-S_{i}\right)-\sum_{i\ !=\ k} y_{i} * \frac{1}{S_{i}} *\left(-S_{i} * S_{k}\right) \\
=-y_{i} *\left(1-S_{i}\right)+\sum_{i\ !=\ k}^{N} y_{i} * S_{k} \\
=-y_{i}+\sum_{i=1}^{N} y_{i} * S_{k} \\
=S_{k}-y_{i}
\end{array}$

### Pixel Shuffle

像素重组，可学习上采样方法，可替代插值

![pixel_shuffle](/img/pixel_shuffle.png)

基于特征抽取和亚像素卷积的方法来扩大特征图

`torch nn.PixelShuffle()`

$N*(C*r*r)*W*H \to N*C*(H*r)*(W*r)$

例：`Pytorch (b, c, h, w)`

特征图 `(n, 256, 64, 64)`，将特征图切分成若干份，每份 `(n, 4, 64, 64)`，`reshape` 成 `(n, 2, 2, 64, 64)`，`(n, 2, 64, 2, 64)`，`(n, 1, 128, 128)`，拼接得到 `(n, 64, 128, 128)`

### 圆上任取三点，钝角三角形概率

圆半径 1，弧 $AB=x,BC=y,AC=2\pi-x-y$

所有可能结果构成的平面区域 $\Omega=\{(x,y)|0<x<2\pi,0<y<2\pi,0<2\pi-x-y<2\pi\}$

锐角或直角三角形构成的平面区域 $A=\{(x,y)|0<x\leq\pi,0<y\leq\pi,0<2\pi-x-y\leq\pi\}$

$P(A)=\frac{1}{4}$

钝角概率 $\frac{3}{4}$

### 5 人 5 顶不同帽子，都戴错概率，N 人呢

错排公式

$D(n)=(n-1)[D(n-1)+D(n-2)]$

$D(1)=0, D(2)=1$

$D(k)=k!N(k)$

$nN(n) = (n-1) N(n-1) + N(n-2)$

$N(n)=\frac{(-1)^2}{2!}+…+\frac{(-1)^{(n-1)}}{(n-1)!}+\frac{(-1)^n}{n!}$

$D(n)=n![\frac{(-1)^2}{2!}+…+\frac{(-1)^{(n-1)}}{(n-1)!}+\frac{(-1)^n}{n!}]$

$P=\frac{11}{30}$

### X, Y 服从均匀分布, max(X,Y) 期望

**均匀分布**

$\max(X, Y)=\frac{1}{2}(X+Y+|X-Y|)$

$\min(X, Y)=\frac{1}{2}(X+Y-|X-Y|)$

$X\sim U[a,b]$

$EX=\frac{a+b}{2}$  $EX^2=\frac{a^2+ab+b^2}{3}$  $DX=EX^2-(EX)^2=\frac{(a-b)^2}{12}$

绘图  $E|X-Y|=2\int_{a}^{b} x\frac{x-a}{(b-a)^2}dx$

**正态分布**

$X\sim N(\mu_1,\sigma_1^2)$  $Y\sim N(\mu_2,\sigma_2^2)$

$X+Y\sim N(\mu_1+\mu_2,\sigma_1^2+\sigma_2^2)$

$X=\mu+\sigma U$  $Y=\mu+\sigma V$  $U,V\sim N(0,1)$

$E(\max(X,Y))=E(\sigma\cdot \max(U,V)+\mu)=\sigma\cdot E(\max(U,V))+\mu$

$E(\max(U,V))=\frac{1}{2}E|U-V|$

$Z=U-V\sim N(0,2)$

$E|U-V|=E|Z|=\int_{-\infty}^{+\infty} |z|f_Z(z)dz=\frac{2}{\sqrt\pi}$

### MLP

Numpy

[https://github.com/hui126/Deep_Learning_Coding](https://github.com/hui126/Deep_Learning_Coding)

### AP    F1score

- code
    
    ```python
    # https://github.com/eriklindernoren/PyTorch-YOLOv3/blob/master/pytorchyolo/utils/utils.py
    # import tqdm
    import numpy as np
    
    def ap_per_class(tp, conf, pred_cls, target_cls):
        """ Compute the average precision, given the recall and precision curves.
        Source: https://github.com/rafaelpadilla/Object-Detection-Metrics.
        # Arguments
            tp:         true positive (np array), true positive 1, false positive 0
            conf:       Objectness value from 0-1 (np array).
            pred_cls:   Predicted object classes (np array).
            target_cls: True object classes (np array).
        # Returns
            The average precision as computed in py-faster-rcnn.
        """
    
        # Sort by objectness
        i = np.argsort(-conf)
        tp, conf, pred_cls = tp[i], conf[i], pred_cls[i]
    
        # Find unique classes
        unique_classes = np.unique(target_cls)
    
        # Create Precision-Recall curve and compute AP for each class
        ap, p, r = [], [], []
        # for c in tqdm.tqdm(unique_classes, desc="Computing AP"):
        for c in unique_classes:
            i = pred_cls == c
            n_gt = (target_cls == c).sum()  # Number of ground truth objects
            n_p = i.sum()  # Number of predicted objects
    
            if n_p == 0 and n_gt == 0:
                continue
            elif n_p == 0 or n_gt == 0:
                ap.append(0)
                r.append(0)
                p.append(0)
            else:
                # Accumulate FPs and TPs
                fpc = (1 - tp[i]).cumsum()
                tpc = (tp[i]).cumsum()
    
                # Recall
                recall_curve = tpc / (n_gt + 1e-16)
                r.append(recall_curve[-1])
    
                # Precision
                precision_curve = tpc / (tpc + fpc)
                p.append(precision_curve[-1])
    
                # AP from recall-precision curve
                ap.append(compute_ap(recall_curve, precision_curve))
    
        # Compute F1 score (harmonic mean of precision and recall)
        p, r, ap = np.array(p), np.array(r), np.array(ap)
        f1 = 2 * p * r / (p + r + 1e-16)
    
        return p, r, ap, f1, unique_classes.astype("int32")
    
    def compute_ap(recall, precision):
        """ Compute the average precision, given the recall and precision curves.
        Code originally from https://github.com/rbgirshick/py-faster-rcnn.
    
        # Arguments
            recall:    The recall curve (np.array).
            precision: The precision curve (np.array).
        # Returns
            The average precision as computed in py-faster-rcnn.
        """
        # correct AP calculation
        # first append sentinel values at the end
        mrec = np.concatenate(([0.0], recall, [1.0]))
        mpre = np.concatenate(([0.0], precision, [0.0]))
    
        # compute the precision envelope
        for i in range(mpre.size - 1, 0, -1):
            mpre[i - 1] = np.maximum(mpre[i - 1], mpre[i])
    
        # to calculate area under PR curve, look for points
        # where X axis (recall) changes value
        i = np.where(mrec[:-1] != mrec[1:])[0]
    
        # and sum (\Delta recall) * prec
        ap = np.sum((mrec[i + 1] - mrec[i]) * mpre[i + 1])
        return ap
    
    if __name__ == '__main__':
        tp = np.array([0, 1, 1, 1, 0, 1, 0])
        conf = np.array([0.45, 0.6, 0.86, 0.94, 0.23, 0.76, 0.34])
        pred_cls = np.array([0, 1, 2, 1, 2, 3, 0])
        target_cls = np.array([3, 1, 2, 1, 0, 3, 1])
        p, r, ap, f1, unique_classes = ap_per_class(tp, conf, pred_cls, target_cls)
        print(p)
        print(r)
        print(ap)
        # print(f1)
        # print(unique_classes)
    ```
    

### AUC

- code
    
    ```python
    def calcAUC(labels, probs):
        P = np.sum(labels == 1)
        N = np.sum(labels == 0)
        neg_prob = []
        pos_prob = []
        for i, label in enumerate(labels):
            if (label == 1):
                pos_prob.append(probs[i])
            else:
                neg_prob.append(probs[i])
        count = 0
        for pos in pos_prob:
            for neg in neg_prob:
                if pos > neg:
                    count += 1
                elif pos == neg:
                    count += 0.5
        return count / (N * P)
    
    from sklearn.metrics import roc_auc_score
    
    labels = np.array([1, 0, 0, 0, 1, 0, 1, 0])
    probs = np.array([0.9, 0.8, 0.3, 0.1, 0.4, 0.9, 0.66, 0.7])
    print(calcAUC(labels, probs))
    print(roc_auc_score(labels, probs))
    ```
    

### Softmax CrossEntropy

softmax 中 `exp` 对很大的数会出现 `inf`，因此要先对每行数字减去每行的最大值，保证 softmax 的 numerical stability
cross_entropy 中 `log(predictions)` 得到 `(3, 3)` 矩阵，`target` 必须也是 `(3, 3)` 才能对应正确相乘，因此 `target` 要事先转成 onehot
`log` 函数输入 `0` 时会出现错误，因此 `log(predictions)` 前要对 `predictions` 进行 `clip`，将其中的 `0` 修改为一个很小的数字

- code
    
    ```python
    # https://zhuanlan.zhihu.com/p/72347472
    import numpy as np
    import torch
    import torch.nn as nn
    
    def softmax(x):
        exp_x = np.exp(x - np.max(x, axis=-1, keepdims=True))
        sm = exp_x / np.sum(exp_x, axis=-1, keepdims=True)
        return sm
    
    def cross_entropy(predictions, targets, epsilon=1e-12):
        predictions = np.clip(predictions, epsilon, 1.-epsilon)
        N = predictions.shape[0]
        ce = -np.sum(targets * np.log(predictions)) / N
        return ce
    
    def to_onehot(label, n_classes):
        N = label.shape[0]
        onehot = np.zeros((N, n_classes))
        for i in range(N):
            onehot[i][label[i]] = 1
        return onehot
    
    if __name__ == '__main__':
        x = np.array([[5, 2.5, 0.1], [4, 0.8, -2], [-2, 1.5, -30]])
        label = np.array([0, 0, 1])
        n_classes = x.shape[1]
        targets = to_onehot(label, n_classes)
        predictions = softmax(x)
        print(predictions)
        ce = cross_entropy(predictions, targets)
        print(ce)
    
        loss = nn.CrossEntropyLoss()
        ce_torch = loss(torch.tensor(x), torch.tensor(label).type(torch.LongTensor))
        print(ce_torch)
    ```
    

### 梯度下降

1. Choose initial guess $x_0$
2. for k = 0, 1, 2, ... do
3.     $s_k=-\nabla f(x_k)$
4.     choose $\alpha_k$ to minimize $f(x_k+\alpha_ks_k)$
5.     $x_{k+1}=x_k+\alpha_ks_k$
6. end for

### NMS

在目标检测网络中，产生 proposal 后使用分类分支给出每个框的每类置信度，使用回归分支修正框的位置，最终使用 NMS 去除同个类别中 IOU 重叠度较高且置信度较低的检测框

1. 将 BBox 按 Score 降序排列
2. 从 Score 最大值开始，保存当前 BBox，依次遍历其后的 BBox，计算与当前 BBox 的 IOU，大于阈值则抑制
3. 遍历一遍后，继续选择下一个非抑制 BBox 重复上述步骤
4. 返回没有被抑制的 BBox
- code
    
    ```python
    def NMS(bboxs, score, thresh):
        x1 = bboxs[:, 0]
        y1 = bboxs[:, 1]
        x2 = bboxs[:, 2]
        y2 = bboxs[:, 3]
        n = bboxs.shape[0]
        area = (x2 - x1 + 1) * (y2 - y1 + 1)
        order = score.argsort()[::-1]
        keep = []
        suppressed = np.array([0] * n)
    
        for i_ in range(n):
            i = order[i_]
            if suppressed[i] == 1:
                continue
            keep.append(i)
            for j_ in range(i_ + 1, n):
                j = order[j_]
                if suppressed[j] == 1:
                    continue
                x_tl = max(x1[i], x1[j])
                y_tl = max(y1[i], y1[j])
                x_br = min(x2[i], x2[j])
                y_br = min(y2[i], y2[j])
                w = max(0, x_br - x_tl + 1)
                h = max(0, y_br - y_tl + 1)
                intersection = w * h
                iou = intersection / (area[i] + area[j] - intersection)
                if iou >= thresh:
                    suppressed[j] = 1
        return keep
    ```
    

### Soft NMS

NMS 会直接删除 IOU 较大的两个真实目标中的一个，造成漏检

SoftNMS 会对得分进行衰减

- code
    
    ```python
    import numpy as np
    
    def softNMS(bboxs, scores, Nt=0.3, sigma=0.5, thresh=0.001, method=2):
        n = bboxs.shape[0]
        x1 = bboxs[:,0]
        y1 = bboxs[:,1]
        x2 = bboxs[:,2]
        y2 = bboxs[:,3]
        areas = (x2 - x1 + 1) * (y2 - y1 + 1)
        indices = np.array([np.arange(n)]).reshape(n,)
    
        for i in range(n):
            tbbox = bboxs[i, :].copy()
            tscore = scores[i].copy()
            tarea = areas[i].copy()
            pos = i + 1
    
            if i != n-1:
                maxscore = np.max(scores[pos:], axis=0)
                maxpos = np.argmax(scores[pos:], axis=0)
            else:
                maxscore = scores[-1]
    
            if tscore < maxscore:
                bboxs[i, :] = bboxs[maxpos + i + 1, :]
                bboxs[maxpos + i + 1, :] = tbbox
                tbbox = bboxs[i, :]
    
                scores[i] = scores[maxpos + i + 1]
                scores[maxpos + i + 1] = tscore
                tscore = scores[i]
    
                areas[i] = areas[maxpos + i + 1]
                areas[maxpos + i + 1] = tarea
                tarea = areas[i]
    
            x_tl = np.maximum(bboxs[i, 0], bboxs[pos:, 0])
            y_tl = np.maximum(bboxs[i, 1], bboxs[pos:, 1])
            x_br = np.minimum(bboxs[i, 2], bboxs[pos:, 2])
            y_br = np.minimum(bboxs[i, 3], bboxs[pos:, 3])
    
            w = np.maximum(0, x_br - x_tl + 1)
            h = np.maximum(0, y_br - y_tl + 1)
            intersection = w * h
            iou = intersection / (areas[i] + areas[pos:] - intersection)
    
            # 0-linear 1-gaussian 2-original nms
            if method == 0:
                weight = np.ones(iou.shape)
                weight[iou > Nt] = weight[iou > Nt] - iou[iou > Nt]
            elif method == 1:
                weight = np.exp(-(iou * iou) / sigma)
            else:
                weight = np.ones(iou.shape)
                weight[iou > Nt] = 0
    
            scores[pos:] = weight * scores[pos:]
    
        indices = indices[scores > thresh]
        keep = indices.astype(int)
    
        return keep
    
    if __name__ == '__main__':
        bboxs = np.array([[200, 200, 400, 400], [220, 220, 420, 420], [200, 240, 400, 440], [240, 200, 440, 400]], dtype=np.float32)
        scores = np.array([0.69, 0.48, 0.87, 0.96], dtype=np.float32)
        keep = softNMS(bboxs, scores, thresh=0.1, method=1)
        print(keep)
    ```
    

### Softer NMS

[](https://arxiv.org/pdf/1809.08545.pdf)

目标检测问题：目标所有预测框均不精确、边界框不确定性（数据标注不确定性、遮挡导致不确定性、数据不确定性）、分类置信度与IoU不强相关

针对分类置信度与IoU不强相关问题，构建IoU置信度

针对预测框不精准，根据IoU置信度加权合并多个框优化预测框

Variance Voting

### Dropout 原理

减轻 CNN 过拟合的正则化方法，Hinton 2012年提出

在一次训练的迭代中，对每一层中的神经元（总数为 $N$）以概率 $P$ 随机剔除，用剩余 $(1-P)×N$ 个神经元所构成的网络来训练本次迭代中的数据（batchsize 个样本）

减少**中间特征**的数量，从而减少冗余，即增加每层各个特征之间的**正交性**

- code
    
    ```python
    # inverted dropout
    def dropout(x, level):
        if level < 0. or level >= 1:
            raise Exception('Dropout level must be in interval [0, 1].')
        retain_prob = 1. - level
        sample = np.random.binomial(n=1, p=retain_prob, size=x.shape)
        x *= sample
        x /= retain_prob //
        return x
    ```
    

`x /= retain_prob` 保持输入输出期望一致 inverted dropout

### DropConnect

丢弃部分网络权重

### Spatial Dropout

随机地对某个特定的维度全部置零，需要指定 Dropout 维度

### L1，L2 正则化原理

线性回归，L1 正则化即 Lasso 回归，L2 正则化即 Ridge 岭回归

正则化通过降低模型的**复杂性**， 达到避免过拟合的效果

L1 使部分权重变为 0，能产生**稀疏性**，可用于**特征选择**

L2 使权重趋于 0，限制权重占比，一般认为参数值小的模型比较简单

复杂模型尝试对所有样本进行拟合，包括**异常点**，造成拟合函数在较小区间内产生较大波动，反映在该区间导数较大，只有较大大参数才可能产生较大导数，加入正则项在原目标函数基础上加入约束，限制模型复杂性

L1 范数符合拉普拉斯分布，不完全可微，表现在图像上会有很多棱角，棱角与目标函数的接触机会远大于其他部分，因此会造成最优值出现在坐标轴上，导致某一维的权重为零 ，产生稀疏权重矩阵，进而防止过拟合

L2 范数符合高斯分布，完全可微，最优值一般不会出现在坐标轴上，在最小化正则项时，权重不断趋向于 0

**L1 正则化**，权重绝对值之和

$L=L(\omega)+\lambda\sum_{1}^{n}{\left | \omega _i \right | }$

$\frac{\partial L}{\partial w_{i}}=\frac{\partial L(w)}{\partial w_{i}}+\lambda \cdot \operatorname{sign}\left(w_{i}\right)$

$\lambda$ 正则化系数 $\eta$ 学习率

$w_{i}=w_{i}-\eta\left(\frac{\partial L(w)}{\partial w_{i}}+\lambda \cdot \operatorname{sign}\left(w_{i}\right)\right)$

**L2 正则化**

$L=L(\omega)+\lambda\sum_{1}^{n}{\omega _i ^2}$

$\frac{\partial L}{\partial w_{i}}=\frac{\partial L(w)}{\partial w_{i}}+2\lambda \cdot w_{i}$

$w_{i}=w_{i}-\eta\left(\frac{\partial L(w)}{\partial w_{i}}+2\lambda \cdot w_{i}\right)=(1-2\eta \lambda)\omega_i-\eta \frac{\partial L(\omega )}{\partial \omega _i}$ 

权重衰减

### 可以用梯度下降训练 SVM ?

[https://zhuanlan.zhihu.com/p/31886934](https://zhuanlan.zhihu.com/p/31886934)

**二分类**模型，定义在特征空间上的间隔最大的线性分类器，核技巧→非线性分类器

可形式化为一个求解凸二次规划的最优化问题，也等价于正则化的合页损失函数的最小化问题，具有**全局最优解**

SVM 本质是带约束的二次规划问题，可以用拉格朗日乘数法或 Hinge Loss 转换为无约束优化问题

分离超平面 $w \cdot x_{i}+b=0$，几何间隔最大的分离超平面唯一

几何间隔：$\gamma_i=y_i(\frac{\omega}{||\omega||}\cdot x_i+\frac{b}{||\omega||})$

最大化所有样本点几何间隔的最小值

支持向量机重要性质：训练完成后，大部分的训练样本都不需要保留，最终模型仅与**支持向量**有关

软间隔，缓解特征空间中**线性不可分**问题

$\min _{w, b, \xi} \frac{1}{2}\|w\|^{2}+C \sum_{i=1}^{N} \xi_{i}$ 

$s.t. \ \ y_i\left(w \cdot x_{i}+b\right) \geq 1-\xi_{i},\ i=1,2, \ldots, N$     $\xi_{i} \geq 0, i=1,2, \ldots, N$

令 $\xi_{i}=\left[1-y_{i}(w \cdot x+b)\right]_{+}, C=\frac{1}{2 \lambda}$

$\min _{w, b} \lambda\left\|w^{2}\right\|+\sum_{i=1}^{N}\left[1-y_{i}(w \cdot x+b)\right]_{+}$ 

分类决策函数

**序列最小最优化（SMO）算法**，包括两部分：求解两个变量二次规划的解析方法和选择变量的启发式方法

Hinge 损失函数保持支持向量机解的稀疏性

核函数，将样本映射到高维特征空间

### 用 Sigmoid 作为激活函数时，为什么用**交叉熵损失函数**，而不用**均方误差损失函数**？

- **均方误差损失函数**：$L=\frac{1}{2n} \sum_{i=1}^{n}\left(f\left(x\right)-y \right)^{2}$

$f(x)=\sigma(z),\ z=wx+b$

$\frac{\partial L}{\partial w} =(f(x)-y)\sigma'(z)x$

$\frac{\partial L}{\partial b} =(f(x)-y)\sigma'(z)$

更新参数 $w$ 和 $b$：

$w=w-\eta \frac{\partial L}{\partial w} =w-\eta (f(x)-y)\sigma'(z)x$

$b=b-\eta \frac{\partial L}{\partial b} =b-\eta (f(x)-y)\sigma'(z)$

$\sigma'$ 在 $z$  取大部分值时很小，导致**参数更新非常慢**

- **交叉熵损失函数**：$L=-\frac{1}{n}\sum_{i=1}^{n}\left[y \log f(x)+\left(1-y\right) \log \left(1-f(x)\right)\right]$

$\frac{\partial L}{\partial f} =-\frac{1}{n}\sum^{n}\frac{\sigma(z)-y}{\sigma(z)(\sigma(z)-1)}$

$\frac{\partial L}{\partial z} =\frac{\partial L}{\partial f} \frac{\partial f}{\partial z} =\frac{\partial L}{\partial f}\sigma'(z)=\frac{\partial L}{\partial f}\sigma(z)(1-\sigma(z))=-\frac{1}{n}\sum^{n}y-\sigma(z)$

$\frac{\partial L}{\partial w} =\frac{\partial L}{\partial z} \frac{\partial z}{\partial w} =(f(x)-y)x$

$\frac{\partial L}{\partial b} =\frac{\partial L}{\partial z} \frac{\partial z}{\partial b} =(f(x)-y)$

更新参数 $w$ 和 $b$：

$w=w-\eta \frac{\partial L}{\partial w} =w-\eta (f(x)-y)x$

$b=b-\eta \frac{\partial L}{\partial b} =b-\eta (f(x)-y)$

参数更新没有 $\sigma'(z)$ 项，**误差大时，权重更新快，误差小时，权重更新慢**

### 对残差网络的理解

当增加网络层数后，网络可以进行更加复杂的特征模式的提取，所以当模型更深时理论上可以取得更好的结果

**退化问题**（Degradation problem）：网络深度增加时，网络准确度出现**饱和**，甚至出现下降

深层网络存在着梯度消失或者爆炸的问题

通过向上堆积新层建立深层网络，一个极端情况是增加的层什么也不学习，仅仅复制浅层网络的特征，即新层是**恒等映射**（Identity mapping），在这种情况下，深层网络应该至少和浅层网络性能一样，也不应该出现退化现象

残差学习解决退化问题

![res.png](/img/res.png)

当输入为 $x$ 时学习到的特征记为 $H(x)$，我们希望其可以学习到残差 $F(x)=H(x)-x$，其原始的学习特征是 $F(x)+x$。残差学习相比原始特征直接学习更容易。当残差为 0 时，此时堆积层仅做恒等映射，至少网络性能不会下降，实际上残差不会为 0，使得堆积层在输入特征基础上学习到新的特征，从而拥有更好的性能

### 梯度消失和梯度爆炸

在比较深的网络中，采用了不合适的激活函数，比如 sigmoid

梯度爆炸一般出现在**深层网络**和**权值初始化值太大**的情况下

链式求导

$y_i=\sigma(z_i)=\sigma(\omega_ix_i+b_i)$

$\frac{\partial C}{\partial b_1} =\frac{\partial C}{\partial y_n}\frac{\partial y_n}{\partial z_n}\frac{\partial z_n}{\partial x_n}\frac{\partial x_n}{\partial z_{n-1}}\frac{\partial z_{n-1}}{\partial x_{n-1}}...\frac{\partial x_{2}}{\partial z_{1}}\frac{\partial z_{1}}{\partial b_{1}}$

$\frac{\partial C}{\partial b_1} =\frac{\partial C}{\partial y_n}\sigma '(z_n)\omega _n \sigma '(z_{n-1})\omega _{n}...\sigma '(z_{1})$

Sigmoid 激活函数 $\frac{1}{1+e^{-x}}$ 导数最大值 $\frac{1}{4}$

层数越多，导数越小，**梯度消失更普遍**

$\omega$ 较大，$\left | \sigma'(z)\omega \right | >1$，梯度爆炸

解决方案：

ReLU, leaky relu 激活函数取代 Sigmoid 激活函数

梯度剪裁、规范

引入 batch normalization、残差结构、梯度正则化

### 过拟合

训练、验证、测试 8:1:1 7:2:1

**交叉验证**

K 折验证  将数据分成 K 个子集，其中一个子集进行验证，其他子集用于训练

**数据增强**

**早停**

验证损失开始增加时，停止训练模型

**移除特征**

移除特征能够降低模型的复杂性，并且在一定程度上避免噪声，使模型更高效。为了降低复杂度，可以移除特征层或减少神经元数量，使网络变小

**正则化**

正则化可用于降低模型的复杂性

损失函数 L1 L2

**Dropout**

