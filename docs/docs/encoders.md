---


layout: default title: Rotary encoder support parent: Porting guide nav\_order: 4 redirect\_from:

- /gettingStarted/encoders.html
- /getting-started/encoders.html

---


# 旋转编码器

Vial实现了旋转编码器的可选GUI配置。这允许用户为编码器的顺时针和逆时针旋转设置单独的键码动作。编码器完全支持QMK层操作，所以在不同的层可以设置不同的键码。

想要让vial支持编码器，你需要将你的键盘移植到Vial。请按照[步骤1](/porting-to-via.md)和[步骤2](/porting-to-vial.md)开始操作。

为了在您的固件中启用编码器支持，请遵循以下步骤。

## 1\.为编码器添加基本的QMK支持

将此添加到你的主`rules.mk`中。

```make
ENCODER_ENABLE = yes
```

在`config.h`中配置编码器对应的引脚。

```c
#define ENCODERS_PAD_A { encoder1a, encoder2a }
#define ENCODERS_PAD_B { encoder1b, encoder2b }
```

更多高级选项遵循QMK对编码器的定义，请参考[QMK文档](https://docs.qmk.fm/#/feature_encoders?id=encoders)，以便全面配置编码器。

##### 如果你已经拥有编码器正常工作的QMK固件，这一步可以跳过。

## 2\.启用QMK编码器键位图

把这些内容添加到Vial keymap文件夹中的`rules.mk`文件。

```make
ENCODER_MAP_ENABLE = yes
```

把这些内容添加到Vial keymap文件夹中的`keymap.c`文件中（两个编码器，四层的例子）。

```c
#if defined(ENCODER_MAP_ENABLE)
const uint16_t PROGMEM encoder_map[][NUM_ENCODERS][2] = {
    [0] =   { ENCODER_CCW_CW(KC_MS_WH_UP, KC_MS_WH_DOWN), ENCODER_CCW_CW(KC_VOLD, KC_VOLU)  },
    [1] =   { ENCODER_CCW_CW(RGB_HUD, RGB_HUI),           ENCODER_CCW_CW(RGB_SAD, RGB_SAI)  },
    [2] =   { ENCODER_CCW_CW(RGB_VAD, RGB_VAI),           ENCODER_CCW_CW(RGB_SPD, RGB_SPI)  },
    [3] =   { ENCODER_CCW_CW(RGB_RMOD, RGB_MOD),          ENCODER_CCW_CW(KC_RIGHT, KC_LEFT) },
};
#endif
```

**重要!** 编码器键位图需要和你的主键盘键位图有相同的层数。

更多`ENCODER_MAP`相关细节请参考[QMK文档](https://docs.qmk.fm/#/feature_encoders?id=encoder-map)。

### Vial与QMK的明显区别

- 在Vial中，层只用数字表示，不能被命名。如果从现有的QMK键位图工作，这些需要改变层命名。
- 编码器键位图***取代了***旧的QMK风格的编码器回调，必须从Vial文件夹的`keymap.c`文件中删除对应内容，才能顺利编译出功能正常的固件。但是，在default文件夹中，相应内容必须保持原样，才能向后兼容QMK。

### 警告!

***不要***编辑这一行中的数字 "2"。

```c
const uint16_t PROGMEM encoder_map[][NUM_ENCODERS][2] = {
```

它和编码器的数量***无关***，表示编码器的两个动作即顺时针和逆时针旋转，编辑它将导致编译失败。

## 3\.添加Vial编码器作为KLE键位图的一部分

在Vial JSON中，编码器被定义为***2个或3个***1u开关，标签代表它可能的动作（顺时针/逆时针旋转）。这些标签是编码器专用的，***并不是***矩阵的一部分。

**没有定义这两个旋转开关会使JSON无效。**

**一个没有可点击按钮的编码器。**

![](../img/encoders-kle.png)

**一个有可点击按钮的编码器。**

![](../img/encoders-kle2.png)

**带有按钮的双编码器。**

![](../img/dual-encoders-kle.png)

**多个编码器与矩阵相结合。**

请注意，在这个例子中，最上面一排标记为红色的按键，需要由编码器的按键来代替。

![](../img/multiple-encoders-kle.png)

### 编码器图例

**中心图例**

- 两个中心为`e`的按键分别对应顺时针和逆时针旋转。

**顶部图例**

- 顶部的数字表示旋钮索引和动作。**不是矩阵的位置，或按键的索引。**这与矩阵是分开的，不应相互混淆。`Encoder Index (0 ->), Rotary action (0 = CCW, 1 = CW)`

**可点击的按钮**

- 底部的1u按键代表实际的旋钮按键，通常是正常矩阵的一部分，**拥有一个正常的索引**，表示它在矩阵中的位置，**与实际的编码器无关**，可以替换成一颗正常的按键。

### 导入KLE

从KLE导出json，在你的文件中引入以下几行，它们与编码器有关（两个编码器的示例）。

```
  ["0,0\n\n\n\n\n\n\n\n\ne",
  "0,1\n\n\n\n\n\n\n\n\ne"],
  
  ["1,0\n\n\n\n\n\n\n\n\ne",
  "1,1\n\n\n\n\n\n\n\n\ne"],
```

用双引号`"`括住所有相关的x w h ，因为KLE json和vial json在符号上有轻微的差异。

## 完整的例子

### **9键宏键盘示例**

建立一个基本的9键宏键盘和一个旋转编码器（没有按钮），将产生以下内容。<sup>[(KLE文件示例)](http://www.keyboard-layout-editor.com/#/gists/f6c1df29df0d44744d9a4dafe26178ef)</sup>

![](../img/basic-91.png)

```json
{
    "name": "NinetyOne",
    "vendorId": "0xXXXX",
    "productId": "0xXXXX",
    "lighting": "none",
    "matrix": {
        "rows": 3,
        "cols": 3
    },
    "layouts": {
        "keymap": [
        [
          { "y": 0.25, "x": 0.5 },
          "0,0\n\n\n\n\n\n\n\n\ne",
          "0,1\n\n\n\n\n\n\n\n\ne"],
        [ {"y": 0.25},
          "0,0", "0,1", "0,2"],
          ["1,0", "1,1", "1,2"],
          ["2,0", "2,1", "2,2"]
        ]
    }
}

```

### 一个有16个键和3个编码器的宏键盘

[KLE文件示例](http://www.keyboard-layout-editor.com/##@@=0,0&=0,1&=0,2&=0,3&_x:0.25%3B&=0,0%0A%0A%0A%0A%0A%0A%0A%0A%0Ae&=0,1%0A%0A%0A%0A%0A%0A%0A%0A%0Ae&_x:0.25%3B&=1,0%0A%0A%0A%0A%0A%0A%0A%0A%0Ae&=1,1%0A%0A%0A%0A%0A%0A%0A%0A%0Ae%3B&@=1,0&=1,1&=1,2&=1,3&_x:0.75%3B&=0,4&_x:1.25%3B&=1,4%3B&@=2,0&=2,1&=2,2&=2,3%3B&@_y:-0.75&x:4.5%3B&=2,0%0A%0A%0A%0A%0A%0A%0A%0A%0Ae&_w:1.75&h:1.75%3B&=2,4&=2,1%0A%0A%0A%0A%0A%0A%0A%0A%0Ae%3B&@_y:-0.25%3B&=3,0&=3,1&=3,2&=3,3)

![](../img/bigmacro.png)

```json
{
    "name": "BIGMAC",
    "vendorId": "0xXXXX",
    "productId": "0xXXXX",
    "lighting": "none",
    "matrix": {
        "rows": 4,
        "cols": 5
    },
    "layouts": {
        "keymap": [
			[
				"0,0","0,1","0,2","0,3",
				
				{"x":0.25},
				"1,0\n\n\n\n\n\n\n\n\ne",
				"1,1\n\n\n\n\n\n\n\n\ne",
			
				{"x":0.25},
				"0,0\n\n\n\n\n\n\n\n\ne",
				"0,1\n\n\n\n\n\n\n\n\ne"
			],
			
			[
				"1,0","1,1","1,2","1,3",
				{"x":0.75},"0,4",
				{"x":1.25},"1,4"
			],
			[
				"2,0","2,1","2,2","2,3"
			],
			[
				{"y":-0.75,"x":4.5},
				"2,0\n\n\n\n\n\n\n\n\ne",
				{"w":1.75,"h":1.75},"2,4",
				"2,1\n\n\n\n\n\n\n\n\ne"],
			[
				{"y":-0.25},"3,0","3,1","3,2","3,3"
			]
        ]
    }
}
```

## 完成!

编译并烧录固件，你应该能够在用户界面中配置编码器。

![](../img/encoders-ui.png)