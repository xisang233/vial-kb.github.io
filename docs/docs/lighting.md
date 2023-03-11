---


## layout: default  
title: Backlight and RGB lighting  
parent: Porting guide  
nav\_order: 5

# 背光和RGB灯效

Vial支持几种QMK灯效模式，包括单色背光、RGB灯效和RGB矩阵。

### RGB矩阵/VialRGB

RGB矩阵是一种QMK灯效模式，适用于实现per-key RGB灯效，以及per-key RGB和underglow的组合。它是大多数情况下的推荐灯效模式。此外，Vial在QMK RGB矩阵的基础上，通过在电脑上运行的脚本提供直接的RGB控制。

为了在Vial中使用RGB矩阵，首先，按照[QMK文档](https://docs.qmk.fm/#/feature_rgb_matrix)来启用和配置该功能。然后，将`VIALRGB_ENABLE = yes`和`"lighting": "vialrgb",`添加到你的`keymaps/vial/vial.json`文件夹。

为了启用所有可用的RGB矩阵效果，请确保你在你的`config.h`文件中定义了RGB矩阵。

```
#define RGB_MATRIX_FRAMEBUFFER_EFFECTS
#define RGB_MATRIX_KEYPRESSES
```

如果你在使用VialRGB直接控制模式时遇到问题（例如内存不足），你可以在`#define VIALRGB_NO_DIRECT`中禁用它。

![](/img/lighting-rgb-matrix.png)

Vial GUI允许配置RGB矩阵效果（效果列表将根据您启用的RGB效果自动构建）、颜色、亮度和速度。此外，还为第三方应用程序提供了一个原始的HID API来直接控制灯光。[这里](https://gist.github.com/xyzz/c91ae462197d4ef30d034bb6ff4c945e)有一个示例脚本。

## 背光

这里的背光是指单色键的背光，通常是通过在每个开关位置焊接2针LED来实现。Vial的背光功能可以让用户控制背光亮度，启用或禁用呼吸背光动画。

![](/img/lighting-backlight.png)

为了使用这个功能，请确保[QMK的背光功能](https://docs.qmk.fm/#/feature_backlight)已经启用(`BACKLIGHT_ENABLE = yes`)。确保在你的`config.h`中有`#define BACKLIGHT_BREATHING`，以便用户可以在Vial GUI中打开和关闭呼吸灯功能。

最后，为了在GUI上激活这个功能，请添加`"lighting": "qmk_backlight",`到你的`vial.json`文件中。<sup>[示例](https://github.com/vial-kb/vial-qmk/blob/2b2ff48c5d8d9b8995ca49eead031ae3691192e1/keyboards/ilumkb/primus75/keymaps/vial/vial.json#L5)</sup>

## RGB灯效

RGB灯效是一种最适合于键盘背光的灯效模式。注意，即使你的键盘只有背光而没有perkey RGB，使用RGB Matrix可能同样更适合你的项目，因为这是一个更强大的功能。

要使用这个功能，首先按照[QMK文档](https://docs.qmk.fm/#/feature_rgblight)启用RGB灯效。然后，添加`"lighting": "qmk_rgblight",`到你的`vial.json`文件中。<sup>[（示例）](https://github.com/vial-kb/vial-qmk/blob/2b2ff48c5d8d9b8995ca49eead031ae3691192e1/keyboards/tw40/keymaps/via/vial.json#L5)</sup>

## 结合背光和RGB灯效

你也可以拥有一个同时实现背光和RGB灯效功能的键盘。首先，按照上面的章节配置QMK固件，使其同时具备这两种功能。然后，在你的`vial.json`文件中设置`"lighting": "qmk_backlight_rgblight",`。<sup>[(示例)](https://github.com/vial-kb/vial-qmk/blob/2b2ff48c5d8d9b8995ca49eead031ae3691192e1/keyboards/yd60mq/keymaps/vial/vial.json#L5)</sup>