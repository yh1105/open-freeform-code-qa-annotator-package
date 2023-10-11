
# Post \#65395378 [Link](https://stackoverflow.com/questions/65395378/)

## How to update a plot in pyqtgraph?

**Vote**: 3 (575/702) **Views**: 8205 (461/702) 

**Internal ID** \#1-3-195

Created at 2020-12-21 15:20:17

Tags: `python` `pyqt5` `pyqtgraph`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am trying to have a user interface using PyQt5 and pyqtgraph. I made two checkboxes and whenever I select them I want to plot one of the two data sets available in the code and whenever I deselect a button I want it to clear the corresponding curve. There are two checkboxes with texts `A1` and `A2` and each of them plot one set of data.
I have two issues:
1- If I select `A1` it plots the data associated with `A1` and as long as I do not select `A2`, by deselecting `A1` I can clear the data associated with `A1`.
However, If I check `A1` box and then I check `A2` box, then deselecting `A1` does not clear the associated plot. In this situation, if I choose to plot random data, instead of a deterministic curve such as `sin`, I see that by selecting either button new data is added but it cannot be removed.
2- The real application have 96 buttons each of which should be associated to one data set. I think the way I wrote the code is inefficient because I need to copy the same code for one button and data set 96 times. Is there a way to generalize the toy code I presented below to arbitrary number of checkboxes? Or perhaps, using/copying the almost the same code for every button is the usual and correct way to do this?
The code is:
```
from PyQt5 import QtWidgets, uic, QtGui
import matplotlib.pyplot as plt
from matplotlib.widgets import SpanSelector
import numpy as np
import sys
import string
import pyqtgraph as pg
from pyqtgraph.Qt import QtGui, QtCore

app = QtWidgets.QApplication(sys.argv)

x = np.linspace(0, 3.14, 100)
y1 = np.sin(x)#Data number 1 associated to checkbox A1
y2 = np.cos(x)#Data number 2 associated to checkbox A2

#This function is called whenever the state of checkboxes changes
def todo():
    if cbx1.isChecked():
        global curve1
        curve1 = plot.plot(x, y1, pen = 'r')
    else:
        try:
            plot.removeItem(curve1)
        except NameError:
            pass
    if cbx2.isChecked():
        global curve2
        curve2 = plot.plot(x, y2, pen = 'y')
    else:
        try:
            plot.removeItem(curve2)
        except NameError:
            pass  
#A widget to hold all of my future widgets
widget_holder = QtGui.QWidget()

#Checkboxes named A1 and A2
cbx1 = QtWidgets.QCheckBox()
cbx1.setText('A1')
cbx1.stateChanged.connect(todo)

cbx2 = QtWidgets.QCheckBox()
cbx2.setText('A2')
cbx2.stateChanged.connect(todo)

#Making a pyqtgraph plot widget
plot = pg.PlotWidget()

#Setting the layout
layout = QtGui.QGridLayout()
widget_holder.setLayout(layout)

#Adding the widgets to the layout
layout.addWidget(cbx1, 0,0)
layout.addWidget(cbx2, 0, 1)
layout.addWidget(plot, 1,0, 3,1)

widget_holder.adjustSize()
widget_holder.show()

sys.exit(app.exec_())
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-12-23 18:12:18

------------

Below is an example I made that works fine.
It can be reused to do more plots without increasing the code, just changing the value of `self.num` and adding the corresponding data using the function `add_data(x,y,ind)`, where `x` and `y` are the values of the data and `ind` is the index of the box (from `0` to `n-1`).
```
import sys
import numpy as np
import pyqtgraph as pg
from pyqtgraph.Qt import QtCore, QtGui

class MyApp(QtGui.QWidget):
    def __init__(self):
        QtGui.QWidget.__init__(self)
        self.central_layout = QtGui.QVBoxLayout()
        self.plot_boxes_layout = QtGui.QHBoxLayout()
        self.boxes_layout = QtGui.QVBoxLayout()
        self.setLayout(self.central_layout)
        
        # Lets create some widgets inside
        self.label = QtGui.QLabel('Plots and Checkbox bellow:')
        
        # Here is the plot widget from pyqtgraph
        self.plot_widget = pg.PlotWidget()
        
        # Now the Check Boxes (lets make 3 of them)
        self.num = 6
        self.check_boxes = [QtGui.QCheckBox(f"Box {i+1}") for i in range(self.num)]
        
        # Here will be the data of the plot
        self.plot_data = [None for _ in range(self.num)]
        
        # Now we build the entire GUI
        self.central_layout.addWidget(self.label)
        self.central_layout.addLayout(self.plot_boxes_layout)
        self.plot_boxes_layout.addWidget(self.plot_widget)
        self.plot_boxes_layout.addLayout(self.boxes_layout)
        for i in range(self.num):
            self.boxes_layout.addWidget(self.check_boxes[i])
            # This will conect each box to the same action
            self.check_boxes[i].stateChanged.connect(self.box_changed)
            
        # For optimization let's create a list with the states of the boxes
        self.state = [False for _ in range(self.num)]
        
        # Make a list to save the data of each box
        self.box_data = [[[0], [0]] for _ in range(self.num)] 
        x = np.linspace(0, 3.14, 100)
        self.add_data(x, np.sin(x), 0)
        self.add_data(x, np.cos(x), 1)
        self.add_data(x, np.sin(x)+np.cos(x), 2)
        self.add_data(x, np.sin(x)**2, 3)
        self.add_data(x, np.cos(x)**2, 4)
        self.add_data(x, x*0.2, 5)
        

    def add_data(self, x, y, ind):
        self.box_data[ind] = [x, y]
        if self.plot_data[ind] is not None:
            self.plot_data[ind].setData(x, y)

    def box_changed(self):
        for i in range(self.num):
            if self.check_boxes[i].isChecked() != self.state[i]:
                self.state[i] = self.check_boxes[i].isChecked()
                if self.state[i]:
                    if self.plot_data[i] is not None:
                        self.plot_widget.addItem(self.plot_data[i])
                    else:
                        self.plot_data[i] = self.plot_widget.plot(*self.box_data[i])
                else:
                    self.plot_widget.removeItem(self.plot_data[i])
                break
        
if __name__ == "__main__":
    app = QtGui.QApplication(sys.argv)
    window = MyApp()
    window.show()
    sys.exit(app.exec_())
```

Note that inside de `PlotWidget` I add the plot using the `plot()` method, it returns a `PlotDataItem` that is saved in the list created before called `self.plot_data`.
With this, you can easily remove it from the `Plot Widget` and add it again. Also if you are aiming for a more complex program, for example, one that you can change the data of each box on the run, the plot will update without major issues if you use the `setData()` method on the `PlotDataItem`
As I said at the beginning, this should work fine with a lot of checkboxes, because the function that is called when a checkbox is Checked/Unchecked, first compare the actual state of each box with the previous one (stored in `self.state`) and only do the changes on the plot corresponding to that specific box. With this, you avoid doing one function for each checkbox and the replot of all de boxes every time you check/uncheck a box (like [user8408080](https://stackoverflow.com/users/8408080/user8408080) did). I don't say it is bad, but if you increase the number of checkboxes and/or the complexity of the data, the workload of replotting all of the data will increase drastically.
The only problem will be when the window is too small to support a crazy amount of checkboxes (96 for example), then you will have to organize the checkboxes in another widget instead of a layout.
Now some screenshots of the code from above:
[](https://i.stack.imgur.com/q87Wh.png)
And then changing the value of `self.num` to `6` and adding some random data to them:
```
self.add_data(x, np.sin(x)**2, 3)
self.add_data(x, np.cos(x)**2, 4)
self.add_data(x, x*0.2, 5)
```

[](https://i.stack.imgur.com/H3hP7.png)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-12-23 15:54:23

------------

In the following I take a more brute force approach, while assuming, that plotting  the curves takes an negligible amount of time:
```
import numpy as np
import sys
import pyqtgraph as pg
from pyqtgraph.Qt import QtGui, QtWidgets

app = QtWidgets.QApplication(sys.argv)

x = np.linspace(0, 3.14, 100)
y1 = np.sin(x)#Data number 1 associated to checkbox A1
y2 = np.cos(x)#Data number 2 associated to checkbox A2

curves = [y1, y2]
pens = ["r", "y"]

#This function is called whenever the state of checkboxes changes
def plot_curves(state):
    plot.clear()
    for checkbox, curve, pen in zip(checkboxes, curves, pens):
        if checkbox.isChecked():
            plot.plot(x, curve, pen=pen)

#A widget to hold all of my future widgets
widget_holder = QtGui.QWidget()

#Making a pyqtgraph plot widget
plot = pg.PlotWidget()

#Setting the layout
layout = QtGui.QGridLayout()
widget_holder.setLayout(layout)

checkboxes = [QtWidgets.QCheckBox() for i in range(2)]
for i, checkbox in enumerate(checkboxes):
    checkbox.setText(f"A{i+1}")
    checkbox.stateChanged.connect(plot_curves)
    layout.addWidget(checkbox, 0, i)

#Adding the widgets to the layout
layout.addWidget(plot, 1, 0, len(checkboxes), 0)

widget_holder.adjustSize()
widget_holder.show()

sys.exit(app.exec_())
```

Now you have a list of checkboxes and the checkbox with index 0 corresponds to the data in the `curves`-list with index 0. I plot all the curves everytime, which yields a little bit more readable code. If this does affect performance, though, this needs to be a little more complicated.
I also tried to add another curve and it seems to work out perfectly fine:
[](https://i.stack.imgur.com/WBN5P.png)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-12-24 14:49:55

------------

I found the problem in your code. Let's see what your code does:

1. When you add the first plot to the widget (either A1 or A2) you get the PlotDataItem and store it in curve1 or curve2. Suppose you check first A1, then your todo function first inspects that the checkbox 1 is Checked, so plot the data and store it in curve1, then the same function inspects the checkbox 2. Checkbox 2 is not checked so the function does the else statement, which removes the curve2 from the plot widget, this variable doesn't exist so it might raise an error, however, you use the try statement and the error never raises.
2. Now, you check the A2 box, your function first inspects checkbox 1, it is checked, so the function will add again the same plot, but as another PlotDataItem, and store it in curve1. Until now, you have two PlotDataItem of the same data (that means two plots) but only the last one is stored in curve1. The next thing the function does is inspect checkbox 2, it is checked so it will plot the second data and save its PlotDataItem in curve2
3. So, when you now uncheck checkbox 1, your function first inspects checkbox 1 (sorry if it is repetitive), it is unchecked, so the function will remove the PlotDataItem stored in curve1 and it does it, but remember you have two plots of the same data, so for us (the viewers) the plot doesn't disappear. That is the problem, but it doesn't end there, the function now inspects checkbox 2, it is checked, so the function will add another PlotDataItem of the second data and stores it in curve2. We again will have the same problem that happened to the first data.


With this analysis, I also learned something, the `PlotDataItem` doesn´t disappear if you "overwrite" the variable in which it is stored, neither it does when it is removed from the `PlotWidget`. Considering that, I did some changes to the code of my previous answer because the old code will create another item each time we check a box that was checked before and was unchecked. Now, if the item is created, my function will add it again, instead of creating another one.
I have some suggestions:
- Try using objects, generate your own widget class. You can avoid calling global variables, passing them as attributes of the class. (Like my previous answer)- If you want to maintain your code as it is (without the use of classes), for it to work, you can add another two variables with the "state" of your checkboxes, so when you call your function first it checks if the state didn´t change and ignore that checkbox. Also, check if the `PlotDataItem` was generated before and only add it again to avoid the generation of more items.- Your objective is to do this with a bunch of boxes or buttons, try using only one variable for all of them: for example, a list, containing all of the boxes/buttons (the objects). Then you can manage any of them by the index. Also, you can do loops over that variable for connecting the objects inside to the same function.```
my_buttons = [ QtGui.QPushButton() for _ in range(number_of_buttons) ]
my_boxes= [ QtGui.QCheckBox() for _ in range(number_of_boxes) ]
my_boxes[0].setText('Box 1 Here')
my_boxes[2].setChecked(True)
for i in range(number_of_boxes):
    my_boxes[i].stateChanged.connect(some_function)
```
- Doing lists of objects also helps you to give names automatically easily:```
my_boxes= [ QtGui.QCheckBox(f"Box number {i+1}") for i in range(number_of_boxes) ]
my_boxes= [ QtGui.QCheckBox(str(i+1)) for i in range(number_of_boxes) ]
my_boxes= [ QtGui.QCheckBox('Box {:d}'.format(i+1)) for i in range(number_of_boxes) ]
```

Finally, here is your code with some small changes to make it work:
```
from PyQt5 import QtWidgets, uic, QtGui
import matplotlib.pyplot as plt
from matplotlib.widgets import SpanSelector
import numpy as np
import sys
import string
import pyqtgraph as pg
from pyqtgraph.Qt import QtGui, QtCore

app = QtWidgets.QApplication(sys.argv)

x = np.linspace(0, 3.14, 100)
y1 = np.sin(x)#Data number 1 associated to checkbox A1
y2 = np.cos(x)#Data number 2 associated to checkbox A2

#This function is called whenever the state of checkboxes changes
def todo():
    global b1st, b2st, curve1, curve2
    if cbx1.isChecked() != b1st:
        b1st = cbx1.isChecked()
        if cbx1.isChecked():
            if curve1 is None:
                curve1 = plot.plot(x, y1, pen = 'r')
            else:
                plot.addItem(curve1)
        else:
            plot.removeItem(curve1)

    if cbx2.isChecked() != b2st:
        b2st = cbx2.isChecked()
        if cbx2.isChecked():
            if curve2 is None:
                curve2 = plot.plot(x, y2, pen = 'y')
            else:
                plot.addItem(curve2)
        else:
            plot.removeItem(curve2)

#A widget to hold all of my future widgets
widget_holder = QtGui.QWidget()

#Checkboxes named A1 and A2
cbx1 = QtWidgets.QCheckBox()
cbx1.setText('A1')
cbx1.stateChanged.connect(todo)
b1st = False
curve1 = None

cbx2 = QtWidgets.QCheckBox()
cbx2.setText('A2')
cbx2.stateChanged.connect(todo)
b2st = False
curve2 = None

#Making a pyqtgraph plot widget
plot = pg.PlotWidget()

#Setting the layout
layout = QtGui.QGridLayout()
widget_holder.setLayout(layout)

#Adding the widgets to the layout
layout.addWidget(cbx1, 0,0)
layout.addWidget(cbx2, 0, 1)
layout.addWidget(plot, 1,0, 3,1)

widget_holder.adjustSize()
widget_holder.show()

sys.exit(app.exec_())
```



------------
    
    