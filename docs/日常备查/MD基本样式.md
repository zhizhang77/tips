# MPE支持的Markdown样式

[TOC]

## 二级标题

### 三级标题[^注]

Typora支持的markdown语法参考[这里][1]和[github][2]

[也可以直接连到外部超链接和本地文件](https://www.baidu.com)

支持  _下杠斜_ __下杠黑__ *星号斜*  **星号黑** _斜体**黑体**嵌套_ <u>下划线</u>  ~~删除~~  ==高亮== `代码`  ~\~下标~ <sub>下标</sub> <sup>上标</sup>

emoji:slightly_smiling_face:可能有兼容性问题:bomb::smile_cat::arrow_left::arrow_right::arrow_upper_left::arrow_upper_right::rocket:

---

## 列表

- 无序列表
- 无序列表

1. 有序列表
2. 有序列表

- [ ] 待办事项
- [x] 待办事项

定义列表
: 第一行是要定义的名词，第二行跟:加空格，列出其定义
: 每个词可以有多种定义


> 引用
>
> > 多级引用
> >
> > - 引用中嵌套其他格式

---

## 表格

| 项目   |   价格 | 数量 |
| ------ | -----: | :--: |
| 计算机 | \$1600 |  5   |
| 手机   |   \$12 |  12  |
| 管线   |    \$1 | 234  |

如果想支持合并单元格，需要在MPW插件的setting中打开enableExtendedTableSyntax

| Column 1 | Column 2 | Column 3 |
| ------ | -----: | :--: |
| A1 |  |
| A2 | > |  C2  |
| > | B3   | C3 |

| Column 1 | Column 2 | Column 3 |
| ------ | -----: | :--: |
| A1 | B1 | C1 |
| ^ | ^ |  C2  |
| ^ | B3   |  |

---

## 嵌入本地和外部图片

![本地链接](./images/lunyu.jpg)

![外部链接](https://www.zybuluo.com/static/img/logo.png)

---

## 代码和公式

### 公式

正文内嵌公式前后用单\$，如$E=mc^2$，独立行公式前后用双\$[^LaTeX]

$$
\Gamma _ { \epsilon } ( x ) = [ 1- e ^ { - 2\pi \epsilon } ] ^ { 1- x } \prod _ { n = 0} ^ { \infty } \frac { 1- \operatorname{exp} ( - 2\pi \epsilon ( n + 1) ) } { 1- \operatorname{exp} ( - 2\pi \epsilon ( x + n ) ) }
$$

$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix}
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
$$

$$
\ce{CH4 + 2 $\left( \ce{O2 + 79/21 N2} \right)$}
$$

### 代码

用'''包围要引用的代码块，并在开头的'''后加语言名以高亮渲染，*不支持其它代码内嵌方式*[^代码]

```python
@requires_authorization
class SomeClass:
    pass

if __name__ == '__main__':
    # A comment
    print 'hello world'
```

---

## 格式转换

### chrome
能用chrome转换的格式优先用chrome，几乎所有插件的格式都可以保留。

### pandoc
主要用来转word，转pdf用chrome更好。如果md包含mermaid，plantUML之类pandoc不支持的插件，可以先转成GFM再用pandoc转word。

需要在md文件开头处加front-matter。例如
```yaml
---
title: "MD格式与图形渲染"
author: John Doe
date: March 22, 2023
output:
  word_document: #输出格式，可以改成pdf_document，rtf_document等等
    # path: /Exports/markdown.docx #缺省与源文件在同一目录
    highlight: "tango" #代码高亮，还可以选zenburn
    toc: true #自动生成目录，md中的toc依然保留
    toc_depth: 2 #目录显示层数
    # reference_docx: mystyles.docx #使用该参考文件的格式设置
    # pandoc_args: ["--csl", "/var/csl/acs-nano.csl"] #定制pandoc的执行参数
---
```
缺省输出的格式比较难看，可以在word或wps中更换主题。

在当前目录中放一个`_output.yaml`，其配置参数会应用于该目录下的所有文件，如：
```yaml
word_document:
  highlight: zenburn
```
那么目录内所有md文件转换时会缺省使用zenburn配色渲染。注意该文件中不需要通常yaml的起始结束标记。

### markdown
这里的markdown指GFM(GitHub Flavored Markdown)，GFM不支持的渲染会被转成图片保存（mermaid之类会需要相应的命令行工具）。可以直接转换，也可以在md文件开头处加front-matter声明配置选项。
```yaml
---
markdown:
  image_dir: /test/assets
  path: /test/output.md
  ignore_from_front_matter: true
  absolute_image_path: false
---
```

---

## 图表

### 流程图用```flow开头[^流程图]

```flow
st=>start: 采购需求:>http://192.168.8.41:8080[blank]
e=>end 
op1=>operation: 打印入库单
op2=>operation: 申请人签收
验收入库
op3=>operation: 领用人提出出库申请
op4=>operation: 采购助理打印出库单:>这里假装是出库单链接
op5=>operation: 领用人签字
op6=>operation: 直属主管签字
para1=>parallel: 物料出库
op7=>operation: 领用人归还
sub1=>subroutine: 采购助理统
计采购清单
sub2=>subroutine: 财务工作

tianbiao=>operation: 填写申请表
并发邮件
cond1=>condition: 有合同
cond2=>condition: 金额>=2000
cond3=>condition: 研发采购
or 行政采购?
gm=>operation: 总经理审批
cto=>operation: CTO审批
admin=>operation: 行政主管审批
io=>inputoutput: 采购员采购
arrive=>operation: 物料到货

st->tianbiao->cond1
cond1(yes,right)->gm
cond1(no,bottom)->cond2
cond2(yes)->gm
cond2(no,bottom)->cond3
cond3(yes)->cto
cond3(no)->admin
gm->io
cto->io
admin->io
io->arrive
arrive->op1

op1->op2->op3->op4->op5->op6->para1
para1(path1)->sub1(right)->sub2(right)->e
para1(path2,right)->op7(top)->op1

```

### 序列图使用```[mermaid][3]语法[^序列图]

```mermaid
%% Example of sequence diagram
  sequenceDiagram
    %% participant add an actor even if it's unused
    participant Tom as Nerd
    par one morning...
    Alice->>Bob: Hello Bob, how are you? # 双>>有箭头
    and at the same time
    Alice -x Tom: ...
    end
    loop Healthcheck
        Bob->Bob: R U OK? # 单>没有箭头
    end
    note over Bob: How am I? # note标注，位置可设为left of，right of和over
    alt is sick
    Bob-xAlice: Not so good :( # x表示异步
    else is well
    Bob-->>Alice: Feeling fresh like a daisy # 双--虚线
    end
    opt Extra response
    Bob-->Alice: Thanks for asking
    end
    Note over Alice,Tom: + and - defines an activation block, so do activate/deactivate
    Tom->>+Alice: How are you?
    Tom->Alice: Are you free today?
    activate Alice
    Note right of Alice: Single > shows no arrow
    deactivate Alice
    Alice-x-Tom: shut up
```

### 甘特图同样用[mermaid][3]语法

```mermaid
    gantt
    title 项目开发流程
    dateFormat  YYYY-MM-DD

    section 项目确定
        需求分析       :done, a1, 2016-06-22, 3d
        可行性报告     :active, after a1, 5d
        概念验证       : 5d
    
    section 项目实施
        概要设计      :crit, 2016-07-05  , 5d
        详细设计      :crit, 2016-07-08, 10d
        编码          :2016-07-15, 10d
        测试          :2016-07-22, 5d
    
    section 发布验收
        发布: 2d
        验收: 12h
```

---

### uml和其它很多类型的图可以用[plantuml][5]

plantuml在Typora中不支持，vscode中的markdown preview enhanced插件可以部分支持但版本不是最新[^json]，[这里][6]有很多用例可以抄

```plantuml
@startuml
scale 550 width
[*] --> NotShooting
state NotShooting {
[*] --> Idle
Idle --> Configuring : EvConfig
Configuring --> Idle : EvConfig
}
state Configuring {
[*] --> NewValueSelection
NewValueSelection --> NewValuePreview : EvNewValue
NewValuePreview --> NewValueSelection : EvNewValueRejected
NewValuePreview --> NewValueSelection : EvNewValueSaved
state NewValuePreview {
State1 -> State2
}
}
@enduml
```

#### 用[plantuml][5]画思维导图
可以用OrgMode或markdown格式，这里以markdown格式为例：
```plantuml
@startmindmap
* root node
	* some first level node
		* second level node
		* another second level node
	* another first level node
@endmindmap
```

### 波形图可以用[plantuml][5]或者[wavedrom][7]实现

#### 用[plantuml][5]画波形图（不如wavedrom，删掉了）
官方说明见 https://plantuml.com/zh/timing-diagram


#### 用[wavedrom][7]画波形图（可以嵌入js）

眼下同样只有markdown preview enhanced插件支持，typora不支持

```wavedrom
{ signal: [
  { name: "pclk", wave: "p.....|..." },
  { name: "Pclk", wave: 'P.....|...' },
  { name: "nclk", wave: 'n.....|...' },
  { name: "Nclk", wave: 'N.....|...' },
  { name: 'clkx', wave: 'phnlzP|HNL' , phase: 0.25},
  {},
  { name: "Data",        wave: "x345x|=.ud", data: ["head", "body", "tail", "data"] },
  { name: "Request",     wave: "0.1..0|1.0" },
  {},
  { name: "Acknowledge", wave: "1.....|01." }
]}
```

```wavedrom
{ assign:[
  ["out",
    ["|",
      ["^", ["~", "a"], "b"],
      ["&", ["~", "b"], "a"]
    ]
  ]
]}
```

```wavedrom
{
  signal:[
    //clock generation
    function clockGen(clockName,repeatNum){
      var clock={name:clockName,wave:'p'+'.'.repeat(repeatNum-1)};
      return clock
    }('clk',16),
    //reset generation
    function resetGen(repeatNum,resetName,resetActiveLevel,resetAssertCycle){
      var reset={name:resetName,wave:''};
      reset.wave+=resetActiveLevel+''+'.'.repeat(resetAssertCycle-1)+(1-resetActiveLevel)+''+'.'.repeat(repeatNum-resetAssertCycle-1);
      return reset;
    }(16,'rst',1,5),
    //data generation
    function dataGen(cycleNum,startCycle,activeCycle,signalNum){
      var signalList=[]
      //signal name generation

      //valid generation
      signalList.push(
        {name:"valid",wave: '0'+ '.'.repeat(startCycle-1) + '1' + '.'.repeat(activeCycle-1) + '0'+'.'.repeat(cycleNum-startCycle-activeCycle-1)}
      );
      //data generation
      for(var signalIndex=0;signalIndex <signalNum;signalIndex++){
        signalList.push({name:"data"+signalIndex+'',wave:'',data:[]} )
        for(var cycleIndex=0;cycleIndex< cycleNum;cycleIndex++){
          signalList[signalIndex+1].wave+=(Math.floor(Math.random() * 8) + 2)+'';
          signalList[signalIndex+1].data.push((cycleIndex*signalNum+signalIndex)+'');
        }
      }
      signalList.unshift('bus');
      return signalList
    }(16,5,6,32)
  ]
}
```

### dot画图
用dot或viz表示的dot语言块在MPE中都能调用viz.js渲染（typora不支持），用`dot {engine="xxx"}`可以指定渲染引擎，支持的渲染引擎包括circo, dot, neato, osage, 和twopi，缺省引擎是dot

```dot
digraph G {
  a -> b
  b -> c
  b -> d
}
```

```dot {engine="twopi"}
digraph G {
  a -> b
  b -> c
  b -> d
}
```

### 统计图用[vega-lite][8]或[vega][9]

眼下同样只有markdown preview enhanced插件支持，typora不支持，vega-lite在MPE上还有问题

```vega
{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "description": "A basic stacked bar chart example.",
  "width": 500,
  "height": 200,
  "padding": 5,

  "data": [
    {
      "name": "table",
      "values": [
        {"x": 0, "y": 28, "c": 0}, {"x": 0, "y": 55, "c": 1},
        {"x": 1, "y": 43, "c": 0}, {"x": 1, "y": 91, "c": 1},
        {"x": 2, "y": 81, "c": 0}, {"x": 2, "y": 53, "c": 1},
        {"x": 3, "y": 19, "c": 0}, {"x": 3, "y": 87, "c": 1},
        {"x": 4, "y": 52, "c": 0}, {"x": 4, "y": 48, "c": 1},
        {"x": 5, "y": 24, "c": 0}, {"x": 5, "y": 49, "c": 1},
        {"x": 6, "y": 87, "c": 0}, {"x": 6, "y": 66, "c": 1},
        {"x": 7, "y": 17, "c": 0}, {"x": 7, "y": 27, "c": 1},
        {"x": 8, "y": 68, "c": 0}, {"x": 8, "y": 16, "c": 1},
        {"x": 9, "y": 49, "c": 0}, {"x": 9, "y": 15, "c": 1}
      ],
      "transform": [
        {
          "type": "stack",
          "groupby": ["x"],
          "sort": {"field": "c"},
          "field": "y"
        }
      ]
    }
  ],

  "scales": [
    {
      "name": "x",
      "type": "band",
      "range": "width",
      "domain": {"data": "table", "field": "x"}
    },
    {
      "name": "y",
      "type": "linear",
      "range": "height",
      "nice": true, "zero": true,
      "domain": {"data": "table", "field": "y1"}
    },
    {
      "name": "color",
      "type": "ordinal",
      "range": "category",
      "domain": {"data": "table", "field": "c"}
    }
  ],

  "axes": [
    {"orient": "bottom", "scale": "x", "zindex": 1},
    {"orient": "left", "scale": "y", "zindex": 1}
  ],

  "marks": [
    {
      "type": "rect",
      "from": {"data": "table"},
      "encode": {
        "enter": {
          "x": {"scale": "x", "field": "x"},
          "width": {"scale": "x", "band": 1, "offset": -1},
          "y": {"scale": "y", "field": "y0"},
          "y2": {"scale": "y", "field": "y1"},
          "fill": {"scale": "color", "field": "c"}
        },
        "update": {
          "fillOpacity": {"value": 1}
        },
        "hover": {
          "fillOpacity": {"value": 0.5}
        }
      }
    }
  ]
}

```

### 其它
GitHub Markdown支持用geojson和topojson显示地图，还可以用stl生成3D模型，见[这里][10]。但MPE都还没有支持。

[^注]: 最多支持六级小标题。
[^LaTeX]: 支持 **LaTeX** 编辑显示，例如：$\sum_{i=1}^n a_i=0$， 访问 [MathJax][4] 参考更多使用方法。

[^代码]: typora支持高亮的语言列表及代码见[这里](http://support.typora.io/Code-Fences-Language-Support/)。
[^流程图]: 用[flowchart.js](https://github.com/adrai/flowchart.js/)渲染，也支持[mermaid][3]的flowchart
[^序列图]: 详细语法见https://github.com/mermaid-js/mermaid/blob/develop/docs/sequenceDiagram.md，另外也支持[js-sequence](https://bramp.github.io/js-sequence-diagrams/)
[^json]: 例如startjson就不支持～

[1]: http://support.typora.io/
[2]: https://help.github.com/en/categories/writing-on-github
[3]: https://mermaid-js.github.io/mermaid/#/
[4]: http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
[5]: https://plantuml.com/zh/
[6]: https://real-world-plantuml.com/
[7]: https://wavedrom.com/tutorial.html
[8]: https://vega.github.io/vega-lite/
[9]: https://vega.github.io/vega/
[10]: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams