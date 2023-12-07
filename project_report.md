# 《Python程序设计基础》程序设计作品说明书

题目： 学生成绩展示程序

学院： 21计科01

姓名： 刘阳阳

学号： B20210302103

指导教师： 周景

起止日期：2023.11.10-2023.12.10

代码仓库地址：[项目地址](https://github.com/last-xuan-official/showGrades)

## 摘要

_介绍本次设计完成的项目的概述，本文的主要内容，总结你主要完成的工作以及关键词。_

鉴于老师没有很好的软件绘图以直观展示学生成绩，我查阅资料开发了一个学生成绩展示程序，该程序可录入数据，以各式各样的图表展示学生的各门成绩，并对成绩区间进行统计。

技术栈：pyqt5(界面)，Qt Designer，css样式，pyqt5.chart(图表)

## 第1章 需求分析

_本章的内容主要包括系统的需求分析，系统主要需要实现的功能有哪些，可以帮助用户解决哪些问题等等。_

#### 功能需求：

1. **数据输入**：允许用户以文本文件、数据库或手动输入的方式添加学生成绩数据。
2. **图表展示**：提供多种图表类型（柱状图、折线图、饼图等）以直观展示学生成绩情况。
3. **过滤和排序**：能够根据课程、学生、成绩等条件进行数据过滤和排序。
4. **数据统计**：计算并展示平均分、最高分、最低分等统计数据。

#### 用户界面需求：

1. **直观易用**：清晰的用户界面，易于操作和导航。
2. **图表交互**：支持图表交互，如悬停显示具体成绩、点击图表元素查看详细信息等功能。
3. **自定义设置**：允许用户调整图表样式、颜色、标签等以满足个性化需求。
4. **主题多样可选**：允许用户对界面进行修改，找到合适自己的主题。
5. **画面动态**：以流畅，简洁的动效进行展示

#### 技术需求：

1. **PyQt应用**：基于PyQt框架开发，确保界面响应和用户体验。
2. **数据处理**：使用适当的数据处理库（如Pandas）来处理和管理成绩数据。
3. **图表库集成**：整合合适的图表库（本项目使用QtChart）以绘制各种图表。

#### 其他需求：

1. **反馈机制**：收集用户反馈并进行改进，以不断提升应用质量。


## 第2章 分析与设计

_本章的内容主要包括系统的设计，例如：系统架构、系统流程、系统模块、数据库的设计，以及关键的实现，例如：使用的数据结果、算法。_

本系统架构以PyQt5为主要框架，有以下组成部分：

1. **GUI 架构**：采用 PyQt 这样的 GUI 库，主界面继承自 QMainWindow，可能包含其他组件或者窗口。需要考虑界面的布局、交互逻辑等。
2. **数据处理**：包括生成数据、统计数据等功能函数，可能需要更深入地了解数据的来源和处理方式。
3. **图表绘制**：包括初始化不同类型的图表以及绘制图表的功能函数，这部分可能涉及到数据与图表的关联，以及图表的显示和更新机制。
4. **事件处理**：按键点击等操作对应的槽函数处理，涉及用户交互方面的逻辑。

***设计实现：***

**1.** 针对GUI架构通过qt designer设计并使用PyUIC将.ui文件转化为.py文件即可实现

**2.** 数据设计模块：

```python
#数据模型来自Qt自带的QStandardItemModel，数据来源是python random生成30-100的数据生成分数，用for循环将分数分配到每个模型数据下
      self.dataModel = QStandardItemModel(self)    #数据模型
      self.ui.tableView.setModel(self.dataModel)   #设置数据模型

      self.dataModel.itemChanged.connect(self.do_calcuAverage) #自动计算平均分
      
      self.__generateData()   #初始化数据
      self.__surveyData()     #数据统计
```

以__generateData方法为例解释数据部分设计具体步骤如下：

- **清空数据模型**：
  - 使用 `self.dataModel.clear()` 清空数据模型。

- **设置表头**：
  - 创建包含学生姓名、数学、语文、英语和平均分的表头列表。
  - 通过 `self.dataModel.setHorizontalHeaderLabels(headerList)` 设置为数据模型的表头文字。

- **生成数据**：
  - 循环生成学生的分数数据。
  - 外部循环控制学生数量，内部循环处理数学、语文和英语三门科目的分数。
  - 为每个学生创建一个包含姓名和三门科目分数的列表（`itemList`）。
  - 生成随机分数在 50 到 100 之间，并将其添加为 `QStandardItem` 对象。
  - 计算每个学生的平均分数，并创建一个平均分数的 `QStandardItem` 对象。
  - 将生成的学生数据列表（包含姓名、各科目分数和平均分）添加到数据模型中，通过 `self.dataModel.appendRow(itemList)` 添加到数据模型中。

```python
   def __generateData(self):  ##随机生成分数数据
      self.dataModel.clear()
      headerList=["姓名","数学","语文","英语","平均分"]
      self.dataModel.setHorizontalHeaderLabels(headerList)  #设置表头文字
      for i in range(self.__studCount):
         itemList=[]
         studName="学生%2d"%(i+1)
         item=QStandardItem(studName)  #创建item
         item.setTextAlignment(Qt.AlignHCenter)
         itemList.append(item)         #添加到列表

         avgScore=0  
         for j in range(self.COL_MATH, 1+self.COL_ENGLISH): #数学，语文，英语
            score=50.0+random.randint(-20,50)
            item=QStandardItem("%.0f"%score)    #创建 item
            item.setTextAlignment(Qt.AlignHCenter)
            itemList.append(item)      #添加到列表
            avgScore =avgScore+score

         item=QStandardItem("%.1f"%(avgScore/3.0))    #创建平均分item
         item.setTextAlignment(Qt.AlignHCenter)
         item.setFlags(item.flags() & (not Qt.ItemIsEditable)) #平均分不允许编辑
         itemList.append(item)   #添加到列表
         self.dataModel.appendRow(itemList)  #添加到数据模型 
```

**3.**图标设计模块:

- 以绘制柱状图方法draw_barChart为例：

***设计实现：***

1. 获取图表对象 `chart` 并清除所有已有的序列和坐标轴。
2. 根据 `isVertical` 参数设置图表的标题和图例位置。
3. 创建三个数据集 `setMath`, `setChinese`, `setEnglish` 分别代表数学、语文和英语科目的成绩，并创建用于显示平均分的折线图序列 `seriesLine`。
4. 通过遍历数据模型获取学生的姓名和各科目成绩，并将其添加到相应的数据集和折线图序列中。
5. 根据 `isVertical` 参数，创建柱状图系列 `seriesBar` 或 `QHorizontalBarSeries` 并将数据集添加到对应的系列中。
6. 设置柱状图数据点标签的可见性和格式，并连接了悬停信号和点击信号的处理函数。
7. 创建学生姓名坐标轴 `axisStud` 和数值型坐标轴 `axisValue`，根据柱状图类型设置柱状图和折线图系列与对应坐标轴的关联。
8. 将图例标记的点击信号连接到处理函数。

```python
 def draw_barChart(self,isVertical=True):  ##绘制柱状图，或水平柱状图
      chart =self.ui.chartViewBar.chart()
      chart.removeAllSeries()          #删除所有序列
      chart.removeAxis(chart.axisX())  #删除坐标轴
      chart.removeAxis(chart.axisY())  #删除坐标轴
      if isVertical: 
         chart.setTitle("Barchart 演示")
         chart.legend().setAlignment(Qt.AlignBottom) 
      else:
         chart.setTitle("Horizontal Barchart 演示")
         chart.legend().setAlignment(Qt.AlignRight) 
         

      setMath = QBarSet("数学")  #QBarSet
      setChinese = QBarSet("语文")
      setEnglish= QBarSet("英语")

      seriesLine = QLineSeries() #QLineSeries序列用于显示平均分
      seriesLine.setName("平均分")
      pen=QPen(Qt.red)
      pen.setWidth(2)
      seriesLine.setPen(pen)

      seriesLine.setPointLabelsVisible(True)    #数据点标签可见
      if isVertical: 
         seriesLine.setPointLabelsFormat("@yPoint")   #显示y数值标签
      else:
         seriesLine.setPointLabelsFormat("@xPoint")   #显示x数值标签

      font=seriesLine.pointLabelsFont()
      font.setPointSize(10)
      font.setBold(True)
      seriesLine.setPointLabelsFont(font)  

      stud_Count=self.dataModel.rowCount()
      nameList=[]        #学生姓名列表,用于QBarCategoryAxis类坐标轴
      for i in range(stud_Count):      #从数据模型获取数据
         item=self.dataModel.item(i,self.COL_NAME)
         nameList.append(item.text())  #姓名,用作坐标轴标签

         item=self.dataModel.item(i,self.COL_MATH)
         setMath.append(float(item.text()))  #数学

         item=self.dataModel.item(i,self.COL_CHINESE)
         setChinese.append(float(item.text()))  #语文

         item=self.dataModel.item(i,self.COL_ENGLISH)
         setEnglish.append( float(item.text())) #英语
         
         item=self.dataModel.item(i,self.COL_AVERAGE)
         if isVertical:
            seriesLine.append(i,float(item.text())) #平均分,用于柱状图
         else:
            seriesLine.append(float(item.text()),i) #平均分，用于水平柱状图
            
      #创建一个序列 QBarSeries, 并添加三个数据集
      if isVertical:
         seriesBar = QBarSeries()   #柱状图
      else:
         seriesBar = QHorizontalBarSeries() #水平柱状图
         
      seriesBar.append(setMath)     #添加数据集
      seriesBar.append(setChinese)
      seriesBar.append(setEnglish)
      seriesBar.setLabelsVisible(True)    #数据点标签可见
      seriesBar.setLabelsFormat("@value") #显示数值标签
      seriesBar.setLabelsPosition(QAbstractBarSeries.LabelsCenter)   #数据标签显示位置
      seriesBar.hovered.connect(self.do_barSeries_Hovered)  #hovered信号
      seriesBar.clicked.connect(self.do_barSeries_Clicked)  #clicked信号

      chart.addSeries(seriesBar)    #添加柱状图序列
      chart.addSeries(seriesLine)   #添加折线图序列

      ##学生姓名坐标轴
      axisStud = QBarCategoryAxis()
      axisStud.append(nameList)  #添加横坐标文字列表
      axisStud.setRange(nameList[0], nameList[stud_Count-1]) #这只坐标轴范围
      
      #数值型坐标轴
      axisValue = QValueAxis()
      axisValue.setRange(0, 100)
      axisValue.setTitleText("分数")
      axisValue.setTickCount(6)  
      axisValue.applyNiceNumbers() 
      #    axisValue.setLabelFormat("%.0f") #标签格式
      #    axisY.setGridLineVisible(false)
      #    axisY.setMinorTickCount(4)
      if isVertical:  
         chart.setAxisX(axisStud, seriesBar)    #seriesBar
         chart.setAxisY(axisValue, seriesBar) 
         chart.setAxisX(axisStud, seriesLine)   #seriesLine
         chart.setAxisY(axisValue, seriesLine)
      else:
         chart.setAxisX(axisValue, seriesBar)   #seriesBar
         chart.setAxisY(axisStud, seriesBar)
         chart.setAxisY(axisStud, seriesLine)   #seriesLine
         chart.setAxisX(axisValue, seriesLine)
         
      for marker in chart.legend().markers():  #QLegendMarker类型列表
         marker.clicked.connect(self.do_LegendMarkerClicked)
```
**4.** 事件处理：通过按键对应clicked(self.function)传递信号到槽函数进行对应处理

```python
@pyqtSlot(int)  ##选择课程
def on_comboCourse_currentIndexChanged(self,index):
    self.draw_pieChart()
```

### UI界面设计：

![Alt text](image-2.png)

## 第3章 软件测试

_本章的内容主要包括以类和函数作为单元进行单元测试，编写的对系统的主要功能的测试用例，以及测试用例执行的测试报告。_

本程序包含两大类：

    1.class QmyMainWindow(QMainWindow)：  #主界面类（继承自QMainWindow）
        def __init__(self, parent=None): #初始化主界面
        #自定功能函数
        def __generateData(self):  ##随机生成分数数据
        def __surveyData(self):  ##统计各分数段人数
        def __iniBarChart(self):   ##初始化柱状图
        def __iniStackedBar(self):    ##初始化堆叠柱状图
        def __iniPercentBar(self):    ##百分比柱状图初始化
        def __iniPieChart(self):  ##饼图初始化
        def __getCurrentChart(self):  ##获取当前QChart对象
        ...

        #与按键连接的槽函数
        def on_toolBtn_GenData_clicked(self):##重新生成数据
        def on_toolBtn_Counting_clicked(self):##重新统计
        def on_comboTheme_currentIndexChanged(self,index):##设置图表主题
        def on_comboAnimation_currentIndexChanged(self,index):##图表动画
        ...

        #绘图函数
        def draw_barChart(self,isVertical=True):  ##绘制柱状图，或水平柱状图
        def draw_stackedBar(self,isVertical=True):   #堆叠柱状图
        def draw_percentBar(self,isVertical=True): #百分比柱状图
        def draw_pieChart(self):  ##绘制饼图

    #测试：
    if  __name__ == "__main__":        #用于当前窗体测试
        app = QApplication(sys.argv)    #创建GUI应用程序
        form=QmyMainWindow()            #创建窗体
        form.show()
        sys.exit(app.exec_())



    2.class QmyChartView(QChartView)    #图表展示类（继承自QchartView，检测鼠标操作接受信号传递槽函数）
        def __init__(self, parent=None):
        def mousePressEvent(self,event):    ##鼠标单击
        def mouseMoveEvent(self,event):  ##鼠标移动
        def mouseReleaseEvent(self,event):  ##鼠标框选放大，右键恢复
        def keyPressEvent(self,event):   ##按键按下


### **源码以及测试视频详见**[项目地址](https://github.com/last-xuan-official/showGrades)

### 主界面：

![Alt text](image.png)

## 结论

_本章的内容主要是对项目的总结，项目主要实现了哪些功能，达到了哪些目标，哪些不足之处，可以如何改进。_

#### 实现功能：

1. 学生成绩根据选择人数随机生成三门成绩的数据，并进行统计分数分布和平均分E
2. 界面主题和图表动效的切换
3. 可以绘制饼图，柱状图，百分比柱状图，条形图来直观展示成绩
4. 实现了图表部分放大和缩小（通过左键按住框选实现，按右键刷新）

#### 不足之处：

1. **数据导出**：缺少用户将图表或数据导出为常见格式（如图片、PDF、Excel等）的功能。
2. **数据导入**：缺少用户通过数据库，excel表，csv,json等常见格式快速录入程序的功能。
3. **数据留存**：没有使用 QtSQL包以保留数据至数据库

#### 改进：

1. **数据导出**：
   - **图表导出**：实现将图表导出为常见格式（如图片、PDF）的功能，利用 PyQt 中的相关模块（如 `QImage` 或 `QPrinter`）将图表转换为图片或 PDF 文件，并提供导出选项给用户。
   - **数据导出**：允许用户将数据导出为 Excel、CSV 或 JSON 格式的文件。使用 Python 库（如 `openpyxl`、`pandas` 等）实现数据的导出功能。

2. **数据导入**：
   - 支持通过数据库（如 SQLite、MySQL 等）、Excel 表格、CSV、JSON 等常见格式快速录入数据。利用 PyQt 中的文件选择器和对应格式的解析库，让用户将外部数据导入到程序中进行处理和展示。

3. **数据留存**：
   - 利用 PyQt 中的 QtSQL 模块或者 Python 数据库模块（如 `sqlite3`、`SQLAlchemy` 等），将数据存储到数据库中。这样能长期保留数据，便于后续访问和处理。需要建立数据库模式以适应数据结构，并编写相应的数据存储和检索功能。

## 参考文献

王维波，栗宝娟，张晓东.Python Qt GUI与数据可视化编程[M].北京:人民邮电出版社,2019.9:P378

Eric Matthes.**Python编程**从入门到实践(第三版)[M].北京：人民邮电出版社，2023.5：P53-57,P94-98