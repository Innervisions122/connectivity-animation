出了一道和 verilog 知识没有半毛钱关系的题目，将治何罪？请听题：

# taffy 接线连通检测

## 1、简介

永雏塔菲正在检查一块实验板。板上有 8 个接线柱，编号为 0 到 7，接线柱之间原本互不连接。现在，她使用 3 根导线连接其中的一些接线柱，每根导线的两端各接在一个接线柱上。

两个接线柱既可以通过一根导线直接连通，也可以通过其他接线柱和多根导线间接连通。例如，一根导线连接 0 和 1，另一根连接 1 和 2，那么 0、1、2 就相互连通。如果无法沿导线从一个接线柱到达另一个接线柱，则它们不连通。

请各位雏草姬设计一个组合逻辑模块，根据这 3 根导线两端的接线柱编号，判断指定的两个接线柱是否连通。三根导线的两端分别由 `(a0, b0)`、`(a1, b1)`、`(a2, b2)` 给出，待查询的两个接线柱编号分别为 `query_u` 和 `query_v`。若它们连通，输出 `connected = 1'b1`；否则输出 `connected = 1'b0`。

额外说明：

1. 每个接线柱都与自身连通，没有连接任何导线的接线柱只与自身连通。
2. 允许一根导线的两端接在同一个接线柱上，此时该导线不影响任何连通性。
3. 三根导线的排列顺序，以及任意一根导线两端的顺序，均不影响连通性。

## 2、模块规格

模块名：`connectivity`。

| 信号名 | 方向 | 描述 |
| --- | --- | --- |
| `a0[2:0]` | I | 第一根导线一端的接线柱编号 |
| `b0[2:0]` | I | 第一根导线另一端的接线柱编号 |
| `a1[2:0]` | I | 第二根导线一端的接线柱编号 |
| `b1[2:0]` | I | 第二根导线另一端的接线柱编号 |
| `a2[2:0]` | I | 第三根导线一端的接线柱编号 |
| `b2[2:0]` | I | 第三根导线另一端的接线柱编号 |
| `query_u[2:0]` | I | 待查询的第一个接线柱编号 |
| `query_v[2:0]` | I | 待查询的第二个接线柱编号 |
| `connected` | O | 两个接线柱是否连通，1 表示连通，0 表示不连通 |

以下是解题代码：

    module connectivity(
        input [2:0] a0, b0,
        input [2:0] a1, b1,
        input [2:0] a2, b2,
        input [2:0] query_u, query_v,
        output reg connected
    );
    
    reg [7:0] reach;
    reg [7:0] next_reach;
    integer i;
    
    always @(*) begin
        // 初始时，query_u 只能到达自身
        reach = 8'b00000001 << query_u;
        next_reach = 8'b0;
    
        // 最多经过3根导线，因此传播3轮即可
        for (i = 0; i < 3; i = i + 1) begin
            next_reach = reach;
    
            // 第一根导线
            if (reach[a0])
                next_reach[b0] = 1'b1;
            if (reach[b0])
                next_reach[a0] = 1'b1;
    
            // 第二根导线
            if (reach[a1])
                next_reach[b1] = 1'b1;
            if (reach[b1])
                next_reach[a1] = 1'b1;
    
            // 第三根导线
            if (reach[a2])
                next_reach[b2] = 1'b1;
            if (reach[b2])
                next_reach[a2] = 1'b1;
    
            reach = next_reach;
        end
    
        connected = reach[query_v];
    end
    
    endmodule
