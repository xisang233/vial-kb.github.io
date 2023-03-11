---


## layout: default  
title: Tap Dance  
parent: User manual  
nav\_order: 4

# Tap Dance

Tap Dance允许你为一个按钮的不同按下方式来配置不同的动作。例如，同一个按钮可以被配置为在点击按钮时激活一个宏，或者在按住按钮时激活其他东西。

## 1\.配置踢踏舞

点击顶****部的******Tap Dance**选项卡和它下面的一个可用数字。默认情况下，可以使用8个Tap Dance配置，但可以降低这个数字以减少固件的尺寸。

![](../img/tap-tabs.png)

每个Tap Dance可以配置为根据按钮的按压方式做不同的动作。在下面的例子中，当按钮被“轻敲”**(按下并快速松开)**时，左GUI被按下，但当按钮被“按住”**（按住不放）**时，按钮会激活第一层。另外两个选项**（On double tap）**和**（On tap + hold）**也可以配置。

![](../img/tap-overview.png)

**tapping term**(敲击时间)设置固件区分这些动作的时间阈值。如果一个按钮保持的时间超过了敲击时间，它就被认为是保持。如果一个按钮被按住的时间短于敲击的时间，它就被认为是一次轻敲。

当你对所有的Tap Dance配置感到满意时，点击**Save**(保存)来保存所有的变化。如果你想恢复到设备上已有的配置，点击**恢复**

## 2\.使用Tap Dance

配置并保存好Tap Dance后，就可以使用了。只要在上面的面板上点击你想用于Tap Dance的键，然后在下面的调色板上选择你的Tap Dance。(在Tap Dance标签下)

![](../img/tap-left-menu-example.png)

# 更多信息

Tap Dance是QMK的一个功能，更详细的信息请参考[QMK官方文档](https://docs.qmk.fm/#/feature_tap_dance)。