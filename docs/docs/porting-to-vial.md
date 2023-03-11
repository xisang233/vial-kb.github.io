---


layout: default title: Build support 2 - Port to Vial parent: Porting guide nav\_order: 3 redirect\_from:

- /gettingStarted/porting-to-vial.html
- /getting-started/porting-to-vial.html

---


> Information {: .label .labelgreen }`vial-qmk`提供了几个设置Vial的示例，覆盖几种最常见主控。你可以在[`vial-qmk/keyboards/vial_example`](https://github.com/vial-kb/vial-qmk/tree/vial/keyboards/vial_example)查看它们。

# 移植到Vial

本教程的第二部分将指导你移植键盘到Vial。

## 1\.克隆Vial QMK分支

Vial目前不包括在QMK的主仓库中。因此，你需要查看Vial的QMK分支`vial-kb/vial-qmk`，并在开始本教程的其余部分之前在那里移植你的键盘。

### 高级指南：

1. 从 [https://github.com/vial-kb/vial-qmk](https://github.com/vial-kb/vial-qmk)克隆最新版本的软件库到一个新的目录；这个目录不需要与QMK主目录相同。如果你遇到困难，请参考[这里](https://docs.qmk.fm/#/newbs_getting_started)的QMK安装指南。
2. 在你的新`vial-qmk`目录下运行`make git-submodule`，克隆git子模块。
3. 继续从这个目录运行`make path/to/your/keyboard:keymap`以编译Vial。确保你的键盘的`default`键位编译成功。例如，如果你的键盘位于`keyboards/xyz/xyz60`文件夹中，要使用`default`键位进行编译，请输入`make xyz/xyz60:default` 。

## 2\.创建一个新的`vial`键盘图

将现有的`keymaps/default`键盘图复制到`keymaps/vial`.

## 3\.在你的规则文件中启用Vial

在下面创建一个新的`[keyboard_name]/keymaps/vial/rules.mk`文件，内容如下<sup>[(示例)](https://github.com/vial-kb/vial-qmk/blob/90f3b0e2e188eccb23ed8a2a690df278a0f1057b/keyboards/vial_example/vial_atmega32u4/keymaps/vial/rules.mk#L2)</sup>

```
VIA_ENABLE = yes
VIAL_ENABLE = yes
```

## 4\.移动JSON，以便Vial能找到它

将你的键盘定义JSON（可以是本教程第1步中制作的，也可以是从[VIA键盘库](https://github.com/the-via/keyboards/tree/master/src)中下载的）放在`[keyboard_name]/keymaps/vial/vial.json`，以便Vial编译过程可以找到它。<sup>[(例子)](https://github.com/vial-kb/vial-qmk/blob/90f3b0e2e188eccb23ed8a2a690df278a0f1057b/keyboards/vial_example/vial_atmega32u4/keymaps/vial/vial.json)</sup>

## 5\.生成并添加唯一的键盘 ID

从vial-qmk的根目录下，运行`python3 util/vial_generate_keyboard_uid.py`以生成一个独特的Vial键盘ID。

```
python3 util/vial_generate_keyboard_uid.py
#define VIAL_KEYBOARD_UID {0xXX, 0xXX, 0xXX, 0xXX, 0xXX, 0xXX, 0xXX, 0xXX}
```

在`[keyboard_name]/keymaps/vial/config.h`下创建一个新的文件，并在其中添加以下内容。

```
/* SPDX-License-Identifier: GPL-2.0-or-later */

#pragma once

#define VIAL_KEYBOARD_UID {0xXX, 0xXX, 0xXX, 0xXX, 0xXX, 0xXX, 0xXX, 0xXX}
```

最后一行应该与你在上一步得到的内容一致。<sup>[(示例)](https://github.com/vial-kb/vial-qmk/blob/90f3b0e2e188eccb23ed8a2a690df278a0f1057b/keyboards/vial_example/vial_atmega32u4/keymaps/vial/config.h#L5)</sup>

## 6\.设置一个安全的解锁组合键

Vial需要一个组合键，以保护用户不被恶意的主机在不知情的情况下改变安全敏感的设置，如刷入一个恶意的固件；更多信息见[这里](security.md)。

如果你不想利用这个功能，你应该在你的`keymaps/vial/rules.mk`中设置`VIAL_INSECURE = yes`。虽然您可以将生成的固件分发给您的用户，并且不会影响任何Vial的功能，但请注意，启用`VIAL_INSECURE`的键盘将不被接受到`vial-qmk`主仓库中。

对于没有定义`VIAL_INSECURE`的键盘，继续进行`VIAL_UNLOCK_COMBO_ROWS`配置和`VIAL_UNLOCK_COMBO_COLS`定义。

* 你应该配置一个至少有两个键的组合
* 假设这是你的KLE，你想配置一个Escape+Enter的组合：![](../img/security-kle.png)
* Escape键位于\[0, 0]，Enter键位于\[2, 13]。
* 应该在`keymaps/vial/config.h`文件中，`VIAL_KEYBOARD_UID`的下一行设置<sup>[（示例）](https://github.com/vial-kb/vial-qmk/blob/90f3b0e2e188eccb23ed8a2a690df278a0f1057b/keyboards/vial_example/vial_atmega32u4/keymaps/vial/config.h#L6-L7)</sup>。
  * `#define VIAL_UNLOCK_COMBO_ROWS { 0, 2 }`
  * `#define VIAL_UNLOCK_COMBO_COLS { 0, 13 }`
* 请注意，这个功能可以在多布局键盘上使用，但是应该确保该按键出现在每个可能的布局中：![](../img/security-user-prompt.png)

在刷入固件后，通过激活 "Security->Unlock" 菜单检查该功能是否正常工作。

## 7\.编译Vial固件

编译和刷写可以用与QMK相同的方式进行。例如，要为位于`keyboards/xyz/xyz60`的键盘编译一个`vial`键位图的键盘，在`vial-qmk`根目录下运行`make xyz/xyz60:vial`。如果在运行时发现空间不够（闪存，RAM或EEPROM），请参阅[本指南](firmware-size.md)，以减少Vial固件的大小。

## 完成!

你现在应该能够编译固件，闪存，并让Vial自动检测到你的键盘。

你遇到了问题吗？你可以通过加入[Vial discord](https://discord.gg/zNKEUXTKwF)获得移植支持。