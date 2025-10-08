Mermaid可以实现很多常用的图表，但因为markdown文件是作为一个整体渲染的，实时渲染太多会严重影响页面打开速度，如果需要在单个文件中插入大量图表，依然建议使用静态图像。

```mermaid
%%{init: {'theme':'neutral'}}%%
  graph LR
    a --> b((b))
```

### 甘特图

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

### 思维导图
直接通过缩进控制层级，可以复用流程图的部分形状符号，label中也可以使用markdown语法：
```mermaid
mindmap
  root((mindmap))
    (Origins)
      {{Long history}}
      ::icon(fa fa-book)
      Popularisation
        book1 ["British popular
         psychology
          *author* __Tony Buzan__"]
    [Research]
      On effectiveness<br/>and features
      On Automatic creation
        Uses
            )Creative techniques(
            Strategic planning
            ))Argument mapping((
    Tools
      Pen and paper
      Mermaid
```

### 行程图
```mermaid
  journey
    title 毕业设计安排
    section 选题阶段
      导师申报论文题目 : 7 : 导师
      学生与导师双选 : 7 : 学生, 导师
      导师下达任务书 : 7 : 导师
    section 调研阶段
      学生搜集资料阅读资料 : 4 : 导师, 学生
      学生上交综述与译文 : 5 : 导师, 学生
    section 开题阶段
      完成开题报告与开题答辩 : 7 : 导师, 学生, 专业系, 学院
    section 撰写阶段
      写完三稿论文和设计说明书，经过三位老师评审 : 6 : 导师, 学生
      论文进度期中检查 : 6 : 专业系, 学院
    section 答辩评定阶段
      组织答辩 : 4 : 导师, 答辩小组, 答辩委员会
      成绩评定 : 3 : 导师, 答辩小组, 答辩委员会, 学院
    section 补充
      二次答辩 : 1 : 学院
    section 资料归档
      收尾工作 : 9 : 学院
```

### 看板图
```mermaid
---
config:
  kanban:
    ticketBaseUrl: 'https://mermaidchart.atlassian.net/browse/#TICKET#'
---
kanban
  Todo
    [Create Documentation]
    docs[Create Blog about the new diagram]
  [In progress]
    id6[Create renderer so that it works in all cases. We also add som extra text here for testing purposes. And some more just for the extra flare.]
  id9[Ready for deploy]
    id8[Design grammar]@{ assigned: 'knsv' }
  id10[Ready for test]
    id4[Create parsing tests]@{ ticket: MC-2038, assigned: 'K.Sveidqvist', priority: 'High' }
    id66[last item]@{ priority: 'Very Low', assigned: 'knsv' }
  id11[Done]
    id5[define getData]
    id2[Title of diagram is more than 100 chars when user duplicates diagram with 100 char]@{ ticket: MC-2036, priority: 'Very High'}
    id3[Update DB function]@{ ticket: MC-2037, assigned: knsv, priority: 'High' }

  id12[Can't reproduce]
    id3[Weird flickering in Firefox]
```

### 数据包图
```mermaid
---
title: "TCP Packet"
---
packet-beta
0-15: "Source Port"
16-31: "Destination Port"
32-63: "Sequence Number"
64-95: "Acknowledgment Number"
96-99: "Data Offset"
100-105: "Reserved"
106: "URG"
107: "ACK"
108: "PSH"
109: "RST"
110: "SYN"
111: "FIN"
112-127: "Window"
128-143: "Checksum"
144-159: "Urgent Pointer"
160-191: "(Options and Padding)"
192-255: "Data (variable length)"
```

### XY图（当下只支持柱状和折线图）
```mermaid
---
config:
    xyChart:
        width: 900
        height: 600
    themeVariables:
        xyChart:
            titleColor: "#ff0000"
---
xychart-beta
    title "Sales Revenue"
    x-axis [jan, feb, mar, apr, may, jun, jul, aug, sep, oct, nov, dec]
    y-axis "Revenue (in $)" 4000 --> 11000
    bar [5000, 6000, 7500, 8200, 9500, 10500, 11000, 10200, 9200, 8500, 7000, 6000]
    line [5000, 6000, 7500, 8200, 9500, 10500, 11000, 10200, 9200, 8500, 7000, 6000]
```


### 象限图
```mermaid
quadrantChart
  title Reach and engagement of campaigns
  x-axis Low Reach --> High Reach
  y-axis Low Engagement --> High Engagement
  quadrant-1 We should expand
  quadrant-2 Need to promote
  quadrant-3 Re-evaluate
  quadrant-4 May be improved
  Campaign A: [0.9, 0.0] radius: 12
  Campaign B:::class1: [0.8, 0.7] color: #ff3300, radius: 10
  Campaign C: [0.7, 0.2] radius: 3, color: #00ff33, stroke-color: #10f0f0
  Campaign D: [0.6, 0.3] radius: 15, stroke-color: #00ff0f, stroke-width: 5px ,color: #ff33f0
  Campaign E:::class2: [0.2, 0.4]
  Campaign F:::class3: [0.4, 0.5] color: #0000ff
  classDef class1 color: #109060
  classDef class2 color: #908342, radius : 10, stroke-color: #310085, stroke-width: 10px
  classDef class3 color: #f00fff, radius : 10
```

### 桑基图
将数据保存为`source,target,value`格式的csv文件，直接粘贴进来就可以。
```mermaid
---
config:
  sankey:
    showValues: false
---
sankey-beta

Agricultural 'waste',Bio-conversion,124.729
Bio-conversion,Liquid,0.597
Bio-conversion,Losses,26.862
Bio-conversion,Solid,280.322
Bio-conversion,Gas,81.144
Biofuel imports,Liquid,35
Biomass imports,Solid,35
Coal imports,Coal,11.606
Coal reserves,Coal,63.965
Coal,Solid,75.571
District heating,Industry,10.639
District heating,Heating and cooling - commercial,22.505
District heating,Heating and cooling - homes,46.184
Electricity grid,Over generation / exports,104.453
Electricity grid,Heating and cooling - homes,113.726
Electricity grid,H2 conversion,27.14
Electricity grid,Industry,342.165
Electricity grid,Road transport,37.797
Electricity grid,Agriculture,4.412
Electricity grid,Heating and cooling - commercial,40.858
Electricity grid,Losses,56.691
Electricity grid,Rail transport,7.863
Electricity grid,Lighting & appliances - commercial,90.008
Electricity grid,Lighting & appliances - homes,93.494
Gas imports,Ngas,40.719
Gas reserves,Ngas,82.233
Gas,Heating and cooling - commercial,0.129
Gas,Losses,1.401
Gas,Thermal generation,151.891
Gas,Agriculture,2.096
Gas,Industry,48.58
Geothermal,Electricity grid,7.013
H2 conversion,H2,20.897
H2 conversion,Losses,6.242
H2,Road transport,20.897
Hydro,Electricity grid,6.995
Liquid,Industry,121.066
Liquid,International shipping,128.69
Liquid,Road transport,135.835
Liquid,Domestic aviation,14.458
Liquid,International aviation,206.267
Liquid,Agriculture,3.64
Liquid,National navigation,33.218
Liquid,Rail transport,4.413
Marine algae,Bio-conversion,4.375
Ngas,Gas,122.952
Nuclear,Thermal generation,839.978
Oil imports,Oil,504.287
Oil reserves,Oil,107.703
Oil,Liquid,611.99
Other waste,Solid,56.587
Other waste,Bio-conversion,77.81
Pumped heat,Heating and cooling - homes,193.026
Pumped heat,Heating and cooling - commercial,70.672
Solar PV,Electricity grid,59.901
Solar Thermal,Heating and cooling - homes,19.263
Solar,Solar Thermal,19.263
Solar,Solar PV,59.901
Solid,Agriculture,0.882
Solid,Thermal generation,400.12
Solid,Industry,46.477
Thermal generation,Electricity grid,525.531
Thermal generation,Losses,787.129
Thermal generation,District heating,79.329
Tidal,Electricity grid,9.452
UK land based bioenergy,Bio-conversion,182.01
Wave,Electricity grid,19.013
Wind,Electricity grid,289.366

```