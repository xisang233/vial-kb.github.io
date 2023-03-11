---


## layout: default  
title: Auto-Populating Tap Dance  
parent: Porting guide  
nav\_order: 7

# 自动填充的Tap Dance

Vial对Tap Dance的逻辑做了一些改变，所以你不能简单地使用[QMK代码](https://docs.qmk.fm/#/feature_tap_dance)来实现。但如果你熟悉使用用户界面设置Tap Dance，你可以编辑你的键位图并让它使用Vial的系统，并在刷写固件后自动填充条目，所以你不必在每次固件更新后手动加载布局。

在我们的例子中，我们想在点击时在各层之间切换，并在按下时发送`CAPS_LOCK`。

在你的`keymap.c`文件中，将键码`TD(0)`添加到布局中的按键动作，在Vial的Tap Dance中从0到31。然后，添加：

```c
void keyboard_post_init_user(void) {
    vial_tap_dance_entry_t td = { TG(1),
                                  KC_CAPSLOCK,
                                  KC_NO,
                                  KC_NO,
                                  TAPPING_TERM };
    dynamic_keymap_set_tap_dance(0, &td); // the first value corresponds to the TD(i) slot
}
```

`vial_tap_dance_entry_t`参数与Vial的用户界面中的参数相同。

![Vial截图](https://user-images.githubusercontent.com/82843921/201093073-968be14a-bba4-4b14-baee-71eb58ae87d2.png)