---


## layout: default  
title: Custom Keycode  
parent: User manual  
nav\_order: 8

## 1.1 Qmk自定义键码

QMK为用户提供了[一种方法](https://github.com/qmk/qmk_firmware/blob/master/docs/custom_quantum_functions.md)来重新定义现有按键的行为或创建新的键码。

例子：这里有3个自定义键码被定义在`keymap.c`中。Enum块被用来给每个键码分配一个唯一的hexcode，每个键码的行为被定义在`process_record_user()`中。然后在PROGMEM keymaps块中分配这些键码。

```c
enum blender_keycode {
    B_VPAN = SAFE_RANGE,  
    B_DOLLY, 
    B_UNDO, 
}

bool process_record_user(uint16_t keycode, keyrecord_t *record) {
	switch (keycode) {
		case B_VPAN:
            if (record->event.pressed) { ... }
            return 0;
        case B_DOLLY:
            if (record->event.pressed) { ... }
            return 0;
        case B_UNDO:
            if (record->event.pressed) { ... }
            return 0;
	}
}
```

当你加载一个带有自定义键码的电路板时，它们将显示为唯一的编码。你可以使用任意键并输入这些`0x5000`数值来分配它们，但这对用户来说不是很友好。

![](../img/vial_before.png)

## 1.2 Vial自定义键码

Via和Vial提供了一种方法来配置自定义键码，这样它们就会作为一个命名的键码出现，并支持自由分配。

在`vial.json`制作一个新`customKeycodes`的列表，其中每个键码有三个属性。

- name: 将出现在按键上的名称，它需要简短，否则文本会溢出。
- title: 一个简短的描述，当你把鼠标悬停在键码上时将会出现。
- shortName:  出现在标题之前。

对于这三个键码，在你的`vial.json`文件中需要添加这样的内容。请注意语法错误。列表中最后一个项目不应该有逗号。

```json
....
"vendorId": "0x0000",
"productId": "0x0000",
"customKeycodes": [
	{"name": "Vpan",
	 "title": "Viewport pan",
	 "shortName": "B_VPAN"
	},
	{"name": "Dolly",
	 "title": "Dolly zoom",
	 "shortname": "B_DOLLY"
	},
	{"name": "Undo",
	 "title": "",
	 "shortName": "B_UNDO"
	}
],
"lighting": "qmk_rgblight",
"matrix": { "rows": 6, "cols": 5 },
....
```

在keymap.c文件中，替换`SAFE_RANGE`为`USER00`

```c
enum blender_keycode {
    B_VPAN = USER00, 
    B_DOLLY, 
    B_UNDO, 
}
```

json文件中的自定义键码__必须__与enum中的内容一致，包括顺序和键码的数量。

如果一切顺利的话，二进制键码将被替换为键码对应的名字。你可以在“用户”选项卡中访问你所有的自定义键码。请注意，固件的大小将略有增加（在这个例子中是~100字节）。

![](../img/vial_after.png)

![](../img/user_tab.png)