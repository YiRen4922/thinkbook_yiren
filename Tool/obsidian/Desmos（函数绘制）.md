### 基本用法
这个插件的基础用法是创建一个带有 `desmos-graph` 标签的代码块，并在代码块主体中放置你要绘制的方程：
```python
```desmos-graph
    y=x
    ```
```
渲染效果如下：
```desmos-graph
y=x
```



### 绘制多个方程
方程使用 LaTeX 数学格式，你可以通过将每个方程放在单独的行来绘制多个方程：

```desmos-graph
y=\sin(x)
y=\frac{1}{x}
```



### 设置图表范围和选项
你可以通过在方程前放置一个 `---` 分隔符来限制图表的范围和应用其他设置。分隔符之前的内容必须是一组 `key=value` 对，可以用**换行符或分号**（或两者都用）分隔：

```desmos-graph
left=0; right=100;
top=10; bottom=-10;
---
y=\sin(x)
```



*   **尺寸**：可以使用 `height` 和 `width` 字段设置渲染图像的尺寸。
*   **网格**：通过设置 `grid=false` 可以禁用图表网格。
*   **三角模式**：使用 `degreeMode` 设置三角函数模式，有效值为 `radians`（弧度，默认）或 `degrees`（角度）。

### 方程控制
你可以为每个方程额外设置三个属性：样式、颜色和限制条件。这些属性必须放在方程后面，用一系列 `|` 字符分隔（顺序不限）。

*   **有效颜色**（不区分大小写）：
    *   `RED`， `GREEN`， `BLUE`， `YELLOW`， `MAGENTA`， `CYAN`， `PURPLE`， `ORANGE`， `BLACK`， `WHITE`
    *   任何以 `#` 开头的十六进制颜色代码（例如 `#42ddf5`）
    *   *注意*：可以在图表设置中使用 `defaultColor` 字段设置默认颜色。

*   **有效样式**（不区分大小写）：
    *   **线**（例如 `y=x`）：`SOLID`（默认）、`DASHED`、`DOTTED`
    *   **点**（例如 `(1,4)`）：`POINT`（默认）、`OPEN`、`CROSS`

**示例**：创建一条绿色的虚线 `x=2`，并限制 `y>0`，以下写法均可：

```desmos-graph
x=2|y>0|green|dashed
```

```desmos-graph
x=2|y>0|dashed|green
```

```desmos-graph
x=2|green|y>0|dashed
```

```desmos-graph
x=2|dashed|green|y>0
```



*   **隐藏方程**：可以使用 `hidden` 标志隐藏单个方程，这在绘制导数等时很有用：

```desmos-graph
    f(x)=x^2|hidden
    f'(x)
```


### 标签
点标签可以用 `label:<内容>` 标志指定（Desmos 不支持方程标签）：

```desmos-graph
    (0,0)|label:(0,0)
    (5,4)|open|label:This is a label
```



---

**原文链接**：[https://github.com/Nigecat/obsidian-desmos](https://github.com/Nigecat/obsidian-desmos)