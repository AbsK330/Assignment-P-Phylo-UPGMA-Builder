# 2026spring-cs201: DS AIgo 

# Assignment P: Phylo-UPGMA Builder

------

*Updated 2026-06-07*  

*Compiled by Kailin Li*

*项目仓库： https://github.com/AbsK330/Assignment-P-Phylo-UPGMA-Builder*

---

## 1. 项目简介

- **项目名称：** 基于 UPGMA 算法的系统发育树构建工具 (Phylo-UPGMA Builder)
- **核心功能：** 实现经典的 UPGMA（非加权组平均法）聚类算法，将物种遗传差异矩阵转化为符合标准 Newick 格式的系统发育树，并进行图形化渲染。
- **选题初衷：** 
  作为生命科学专业的学生，我希望将本学期《数据结构与算法》中所学的树论、递归及矩阵操作应用于生物学实际问题中。系统发育树是理解生物演化关系的核心工具，通过手动实现 UPGMA 算法，我得以深度内化算法复杂度分析、动态数据结构维护以及工程化开发的完整流程。

---

## 2. 数算知识点应用

1.  **二叉树（Binary Tree）：** 系统发育树本质上是一棵具有进化距离权重的二叉树。本项目通过自定义 `TreeNode` 类手动构建树形结构，而非直接调用外部库。
2.  **递归算法（Recursion）：** 
    - **树的构建：** 采用自底向上的递归策略合并簇。
    - **序列化输出：** 实现了递归版本的 `to_newick` 算法，将树状拓扑结构深度优先遍历（DFS）并序列化为标准文本协议。
3.  **动态矩阵维护：** 在每一轮聚类中，涉及 $N \times N$ 维矩阵的降维操作与动态索引重映射。本项目通过列表推导式高效实现了矩阵的更新与节点的动态移除。
4.  **鲁棒性设计（Robustness）：** 代码中加入了异常捕获机制（Try-Except），能够自动检测运行环境。在第三方生物信息学库（Biopython）缺失的情况下，程序可平滑切换至标准 Newick 文本输出，确保工程的高可用性。

---

## 3. 核心算法说明

### 3.1 算法原理

**UPGMA** (Unweighted Pair Group Method with Arithmetic Mean) 是一种经典的自底向上聚类算法。它基于**分子钟假设**，即认为在进化过程中各谱系的进化速率是恒定的，从而构建出一棵具有等距性质的超度量树。

### 3.2 计算流程

1.  **初始化：** 将 $N$ 个物种视为 $N$ 个独立的簇（Cluster），每个簇对应一个叶子节点。
2.  **寻找最优解：** 在遗传距离矩阵 $D$ 中搜索非对角线上的最小值 $d_{ij}$。这代表簇 $C_i$ 和 $C_j$ 在进化关系上最近。
3.  **节点合并：**
    - 创建新父节点 $C_u$，其高度设为 $d_{ij} / 2$。
    - 计算分支长度：子节点到父节点的距离等于它们之间的高度差。
4.  **矩阵更新：**
    计算新簇 $C_u$ 与任何剩余簇 $C_k$ 之间的距离：
    $$D(u, k) = \frac{D(i, k) \times |C_i| + D(j, k) \times |C_j|}{|C_i| + |C_j|}$$
    其中 $|C_i|$ 代表簇 $i$ 包含的原始物种数量。
5.  **终止条件：** 重复上述步骤，直至所有节点合并为单一根节点。

### 3.3 复杂度分析

- **时间复杂度：** $O(N^3)$。每轮迭代需遍历 $O(N^2)$ 的矩阵，共需进行 $N-1$ 轮合并。
- **空间复杂度：** $O(N^2)$。主要开销为存储遗传距离矩阵。

---

## 4. 运行指南

### 4.1 环境准备

建议运行环境：Python 3.10 - 3.13（由于 NumPy 兼容性原因，暂不建议使用 3.14 预览版）。

**安装依赖库：**

```bash
pip install -r requirements.txt
```

*依赖项说明：`numpy` (矩阵支持), `biopython` (树结构处理), `matplotlib` (图形化渲染)*

### 4.2 运行方式

项目支持标准输入重定向，推荐使用方式 A 以快速验证。

- **方式 A（推荐）：** 

  ```bash
  Windows (CMD): python src/main.py < tests/test1.txt
  Windows (PowerShell): Get-Content tests/test1.txt | python src/main.py
  Linux/Mac: python3 src/main.py < tests/test1.txt
  ```

- **方式 B（交互式）：** 
  运行 `python main.py` 后粘贴数据，Windows 按 `Ctrl+Z` 结束，Linux/Mac 按 `Ctrl+D` 结束。

### 4.3 输入/输出示例

**输入：**

```text
3
Human Chimp Gorilla<img width="1000" height="600" alt="Figure_1" src="https://github.com/user-attachments/assets/b13b75e0-3256-4c21-899c-8e4643291884" />

0.0 2.0 5.0
2.0 0.0 5.0
5.0 5.0 0.0
```

**输出结果：**

- **Newick 文本：** `(Gorilla:2.5000,(Human:1.0000,Chimp:1.0000):1.5000);`
- **可视化图形：** 弹出 Matplotlib 渲染的系统发育树拓扑窗口。
<img width="1000" height="600" alt="result1" src="https://github.com/user-attachments/assets/96943ea3-7ed2-4e05-8e1d-ec8e6ed43f21" />

---

## 5. 项目核心截图
本项目通过多个模拟实验验证了 UPGMA 算法的有效性：

- **测试用例1（灵长类模型）：** 成功还原了人与黑猩猩的姐妹群关系，验证了算法处理真实生物学数据的可靠性。
<img width="1000" height="600" alt="result1" src="https://github.com/user-attachments/assets/1880b6ad-e9b5-4f4a-b573-936c6e0d9144" />

- **测试用例4（外群模型）：** 通过引入高差异度物种，证明了程序在识别进化树根节点（Rooting）时的鲁棒性。
<img width="1000" height="600" alt="resul4" src="https://github.com/user-attachments/assets/763beeb7-c595-4d3c-86a4-62ab32d3b78b" />


- **测试用例5（科级分化模型）：** 准确划分了猫科与犬科两个独立簇，证明了算法在处理多层次分化结构时的逻辑准确性。
<img width="1000" height="600" alt="resul5" src="https://github.com/user-attachments/assets/b525ad6f-17fa-4e45-b56a-33ee8a01373a" />

- **测试用例7（精细演化模型）：** 验证了程序在处理极小遗传距离（如病毒亚种）时的数值精度。
<img width="1000" height="600" alt="resul7" src="https://github.com/user-attachments/assets/0fed4539-6114-4f60-844d-bc3fd0523e7e" />

---

## 6. 学术诚信与 AI 工具声明

1.  **独立实现部分：** 核心数据结构 `TreeNode`、UPGMA 聚类逻辑、Newick 格式化算法、距离矩阵动态维护逻辑均由本人实现并验证。
2.  **AI 辅助说明：** 使用 Gemini协助进行了UPGMA大框架提供，环境配置异常（如 Python 3.14 下的 NumPy 报错）的排查，并辅助完成了可视化模块的集成及本文档的润色。
3.  **引用声明：** 算法实现参考了经典的生物信息学原理，未引用任何开源的 UPGMA 第三方实现包。

---
