---


## layout: default  
title: MIDI support  
parent: Porting guide  
nav\_order: 6

# MIDI

MIDI可以用来通过USB MIDI接口发送MIDI命令。

**QMK**拥有两个MIDI系统，分别是BASIC和ADVANCED。

在MIDI **BASIC**系统中，你只能使用音符键码（红色多边形内的键），而不能使用其他键码，如八度移动、通道变化、调制等。

![](../img/vial-midi.png)

为了在你的固件中启用MIDI支持，请遵循以下步骤。

## 1\.将QMK MIDI支持添加到`config.h`

按照[QMK文档](https://docs.qmk.fm/#/feature_midi)配置MIDI。注意，你不需要实现任何回调，只需要配置文件`config.h`和`rules.mk`。

## 2\.在`rules.mk`启用MIDI

添加`MIDI_ENABLE = yes`到你的`keymaps/vial/rules.mk`文件中。

## 3\.将Vial MIDI作为KLE键位图的一部分。

要在Vial上启用**BASIC**或**ADVANCED MIDI，**必须在`vial.json`文件中加入以下几行： 

```
{ "vial": {
        "midi": "basic"
        ...
    }
}

```

或者

```
{ "vial": {
        "midi": "advanced"
        ...
    }
}
```

## 完成!