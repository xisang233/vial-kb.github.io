---


## layout: default  
title: Reducing firmware size  
parent: Porting guide  
nav\_order: 6

# Reducing firmware size

默认情况下，Vial启用了所有支持的功能。因此，在低端芯片上，如AVR系列，你可能会遇到闪存、RAM或EEPROM不足的情况。

```
quantum/dynamic_keymap.c:102:1: error: static assertion failed: "Dynamic keymaps are configured to use more EEPROM than is available."
_Static_assert(DYNAMIC_KEYMAP_EEPROM_MAX_ADDR >= DYNAMIC_KEYMAP_MACRO_EEPROM_ADDR + 100, "Dynamic keymaps are configured to use more EEPROM than is available.");
```

```
 * The firmware is too large! 30768/28672 (2096 bytes over)
 [ERRORS]

make[1]: *** [tmk_core/rules.mk:469: check-size] Error 1
make: *** [Makefile:530: yd60mq:vial] Error 1
Make finished with errors
```

如果你的键盘发生这种情况，你可以尝试利用下面的选项使主控可以放下固件。

### 启用LTO

要启用LTO，请在你的`keymaps/vial/rules.mk`文件中添加下面这一行：

```
LTO_ENABLE = yes
```

LTO使编译器在优化你的代码时更加努力，从而使固件尺寸更小；它有极小概率破坏某些固件功能并导致运行出错。请确保在启用此选项后测试你的键盘固件。

### QMK设置

要关闭这个功能，请在你的`keymaps/vial/rules.mk`文件中添加下面这一行：

```
QMK_SETTINGS = no
```

### 动态Tap Dance

为了减少RAM和EEPROM的使用，你可以在你的`config.h`文件中添加下面这一行: `#define VIAL_TAP_DANCE_ENTRIES 4`.

要关闭这个功能，请在你的`keymaps/vial/rules.mk`文件中添加下面这一行：

```
TAP_DANCE_ENABLE = no
```

### 动态组合键

为了减少RAM和EEPROM的使用，你可以在你的`config.h`文件中添加下面这一行: `#define VIAL_COMBO_ENTRIES 4`.

要关闭这个功能，请在你的`keymaps/vial/rules.mk`文件中添加下面这一行：

```
COMBO_ENABLE = no
```

### 动态键值覆盖

为了减少RAM和EEPROM的使用，你可以在你的`config.h`文件中添加下面这一行: `#define VIAL_KEY_OVERRIDE_ENTRIES 4`.

要关闭这个功能，请在你的`keymaps/vial/rules.mk`文件中添加下面这一行：

```
KEY_OVERRIDE_ENABLE = no
```

### 减少动态键盘层数

如果你的EEPROM空间不够，你可以减少动态键盘图层的数量。默认的层数是4。要减少它，请在`config.h`文件中定义。

```
#define DYNAMIC_KEYMAP_LAYER_COUNT 2
```

# 更换引导程序

```
 * The firmware is too large! 30768/28672 (2096 bytes over)
 [ERRORS]

make[1]: *** [tmk_core/rules.mk:469: check-size] Error 1
make: *** [Makefile:530: yd60mq:vial] Error 1
Make finished with errors
```

**如果你已经完成了所有上述设置，但当你在AVR平台（比如较流行的使用Atmega32u4芯片的Pro Micro开发板）上编译固件时仍然看到这个消息，你可能需要打破常规，尝试更换bootloader。**

几乎所有这些控制器都是自带Caterina引导程序的。虽然这个引导程序非常稳定，而且在所有情况下都能**正常工作**！但这些代码已经相当老旧，而且占用空间很大，多达~3500字节，导致你无法放下程序。它的大部分功能是你永远也用不到的。

可以把你的bootloader改成一个更现代、更精简的bootloader，它只做需要的事情，不做其他事情，可以为你的keymap和功能节省大量的代码空间。

### 潜在的弊端

**改变引导程序使你的固件有点 "非标准"**，所以如果希望分享它们，那么其他用户也必须换成你选择的引导程序。它还要求你使用ISP编程器（或另一个arduino）用bootloader对控制器进行一次烧录，然后才可以像平常一样通过USB进行烧录。(这与在Pro Micro坏了的情况下恢复引导程序非常相似）。

### 这样做的好处是什么？

程序空间，宝贵的程序空间!

### 推荐什么引导程序？

下面几个引导程序应该都能正常使用，而且可以节省大小不等的空间。有些会增加你可能喜欢的功能，有些则是更加精简。

* [LUFA-DFU](https://github.com/abcminiuser/lufa)是一个流行的，比Caterina略小，并增加了一些功能（大小取决于你启用了哪些功能）的引导程序。
* [QMK-DFU](https://github.com/qmk/lufa)是一个由QMK团队维护的分支，同样增加了一些功能。
* [nanoBoot](https://github.com/osamuaoki/nanoBoot)是一个*迷你*的，仅512字节的HID引导程序。它只做*一*件**_*事*_，就是当你按住复位键并插入USB线时进入引导程序/刷新，和Caterina的方式完全一样。