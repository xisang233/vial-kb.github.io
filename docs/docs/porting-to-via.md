---


layout: default title: Build support 1 - Create JSON parent: Porting guide nav\_order: 2 redirect\_from:

- /gettingStarted/porting-to-via.html
- /getting-started/porting-to-via.html

---


> Information {: .label .labelgreen }`vial-qmk`提供了几个设置Vial的示例，覆盖几种最常见主控。你可以在[`vial-qmk/keyboards/vial_example`](https://github.com/vial-kb/vial-qmk/tree/vial/keyboards/vial_example)查看它们。

# 创建键盘定义JSON

移植Vial的第一步是准备一个描述键盘布局的JSON文件。如果你的键盘已经有了VIA的移植，那么可以直接从[VIA资源库](https://github.com/the-via/keyboards/tree/master/src)下载键盘定义，并直接进入[第二步](/porting-to-vial.md)：添加Vial支持。否则，请按照下面的步骤进行。

## 1\.准备键盘布局

### 创建一个物理布局

键盘定义布局基于KLE(KeyboardLayoutEditor网站)数据，数据中包括其他信息。进入[http://www.keyboardlayouteditor.com/](http://www.keyboard-layout-editor.com/)，创建一个键盘物理布局的图例。使用"Tools -> Remove Legends” 按钮，即可清理全部文字。

![](../img/kle-empty.png)

接下来，把开关矩阵信息写入键盘布局。每个键的左上角图例用于识别其在矩阵中的位置，编码为`row,col` 。有两种方法来准备这些数据：一是来自键盘PCB和原理图文件，二是来自QMK布局宏。

### 遵循现有的QMK布局

如果键盘已经有一个QMK端口，你可以按照`LAYOUT`宏来为KLE中的按键分配行和列。例如，让我们看一下[m0110\_usb](https://github.com/vial-kb/vial-qmk/blob/12950db4d8ec1f294b1285e9b554a8fdc0a4bc6d/keyboards/converter/m0110_usb/m0110_usb.h#L69-L90)。

```
#define LAYOUT_ansi( \
    K32, K12, K13, K14, K15, K17, K16, K1A, K1C, K19, K1D, K1B, K18, K33,  K47, K68, K6D, K62, \
    K30, K0C, K0D, K0E, K0F, K11, K10, K20, K22, K1F, K23, K21, K1E,       K59, K5B, K5C, K4E, \
    K39, K00, K01, K02, K03, K05, K04, K26, K28, K25, K29, K27,      K24,  K56, K57, K58, K66, \
    K38, K06, K07, K08, K09, K0B, K2D, K2E, K2B, K2F, K2C,           K4D,  K53, K54, K55, K4C, \
    K3A, K37,                K31,                K34, K2A, K46, K42, K48,  K52,      K41 \
) { \
    { K00, K01, K02, K03, K04, K05, K06, K07 }, \
    { K08, K09, XXX, K0B, K0C, K0D, K0E, K0F }, \
    { K10, K11, K12, K13, K14, K15, K16, K17 }, \
    { K18, K19, K1A, K1B, K1C, K1D, K1E, K1F }, \
    { K20, K21, K22, K23, K24, K25, K26, K27 }, \
    { K28, K29, K2A, K2B, K2C, K2D, K2E, K2F }, \
    { K30, K31, K32, K33, K34, XXX, XXX, K37 }, \
    { K38, K39, K3A, XXX, XXX, XXX, XXX, XXX }, \
    { XXX, K41, K42, XXX, XXX, XXX, K46, K47 }, \
    { K48, XXX, XXX, XXX, K4C, K4D, K4E, XXX }, \
    { XXX, XXX, K52, K53, K54, K55, K56, K57 }, \
    { K58, K59, XXX, K5B, K5C, XXX, XXX, XXX }, \
    { XXX, XXX, K62, XXX, XXX, XXX, K66, XXX }, \
    { K68, XXX, XXX, XXX, XXX, K6D, XXX, XXX } \
}
```

宏的第一部分显示了类似于KLE中应该有的布局。

<pre>
    <b>K32</b>, K12, K13, K14, K15, K17, K16, K1A, K1C, K19, K1D, K1B, K18, K33,  K47, K68, K6D, K62, \
    K30, K0C, K0D, K0E, K0F, K11, K10, K20, K22, K1F, K23, K21, K1E,       K59, K5B, K5C, K4E, \
    K39, K00, K01, K02, K03, K05, K04, K26, K28, K25, K29, K27,      K24,  K56, K57, K58, K66, \
    K38, K06, K07, K08, K09, K0B, K2D, K2E, K2B, K2F, K2C,           K4D,  K53, K54, K55, K4C, \
    K3A, K37,                K31,                K34, K2A, K46, K42, K48,  K52,      K41 \
</pre>
宏的第二部分定义了每个特定的键如何转化为行和列的位置。

<pre>
    { K00, K01, K02, K03, K04, K05, K06, K07 }, \
    { K08, K09, XXX, K0B, K0C, K0D, K0E, K0F }, \
    { K10, K11, K12, K13, K14, K15, K16, K17 }, \
    { K18, K19, K1A, K1B, K1C, K1D, K1E, K1F }, \
    { K20, K21, K22, K23, K24, K25, K26, K27 }, \
    { K28, K29, K2A, K2B, K2C, K2D, K2E, K2F }, \
    { K30, K31, <b>K32</b>, K33, K34, XXX, XXX, K37 }, \
    { K38, K39, K3A, XXX, XXX, XXX, XXX, XXX }, \
    { XXX, K41, K42, XXX, XXX, XXX, K46, K47 }, \
    { K48, XXX, XXX, XXX, K4C, K4D, K4E, XXX }, \
    { XXX, XXX, K52, K53, K54, K55, K56, K57 }, \
    { K58, K59, XXX, K5B, K5C, XXX, XXX, XXX }, \
    { XXX, XXX, K62, XXX, XXX, XXX, K66, XXX }, \
    { K68, XXX, XXX, XXX, XXX, K6D, XXX, XXX } \
</pre>
例如，以左上角的键为例。在第二个数组中搜索`K32`，我们发现它位于第6行和第2列（从零开始计算）。因此，在KLE中相应键的左上角写入`6,2`。

### 遵循PCB原理图

另外，你也可以按照键盘的PCB原理图来确定按键的行和列位置。

![](../img/kicad1.png) ![](../img/kicad2.png)

例如，可以确定这里的Tab键是K\_15。在原理图中，它被连接到第1行和第0列。因此，它的左上角图例应该被设置为`1,0` 。请注意，第一行从0开始：如果在你的原理图中，第一行和第一列被标记为row1/col1，那么需要从每个数字中减去1再输入。

![](../img/kle-some.png)

### 完成布局

通过填写每个键的数据来完成布局。

![](../img/kle-complete.png)

### 创建布局选项

对于具有多种布局选项的键盘，如支持ISO Enter或不同的最后一排，你可以配置额外的布局选项，这些选项将显示在GUI中，并改变键盘对用户的显示方式。

* 为选项创建标签。你可以选择使用布尔（开/关）选项，或选择（选择框）选项。布尔型选项用一个字符串表示，选择型选项用一个选项列表表示，第一个元素是标题。这些标签将在将来的步骤中使用。例如：

<table>
<tr>
<td>
<pre>
"labels":[
    "Split Backspace",
    "ISO Enter",
    "Split Left Shift",
    "Full Right Shift",
    [
        "Bottom Row",
        "WKL",
        "Blockerless",
        "MX HHKB",
        "True HHKB"
    ]
],
</pre>
</td>
<td><img src="../img/layout-options-sample.png"></td>
</tr>
</table>
* 在KLE中加入布局选项。布局选项放在KLE中按键的右下角。一个选项是两个用逗号隔开的数字，第一项是从0开始的索引（本例中为0）: "Split Backspace"(分裂退格), ..., 4: "Bottom Row"(底行)), 第二个是选项选择。
* 布局选项键是独立的键，通常位于默认选项的一侧。你可以移动整个键盘，以便为布局选项键腾出空间。
* 例如：

\| ![](../img/layout-options-lshift.png)  \| 这里配置索引2("Split Left Shift",分裂左Shift)的选项。当该选项被启用（1）时，"2,1"表示的键就会被激活。当该选项被禁用时（0），"2,0"表示的键被激活。  \| \| [![](../img/layout-options-bottom-row.png)](../img/layout-options-bottom-row.png)  \|这里配置索引4（"Bottom Row",底行）的选项。所有不同的选择（"WKL": "4,0"; "Blockerless": "4,1"; "MX hhkb": "4,2"；"True HHKB"。"4,3"）设置为独立的行。注意，decal(透明)键是用来代替空格的。[![](../img/layout-options-decal.png)](../img/layout-options-decal.png)  \|

* 确保可选键具有相同的边界框。例如，如果你的左Shift设置为2.25u的键，那么分割的左Shift应该有1.25u+1u的键，中间没有任何空间。如果底排的总尺寸是15u，那么每一个底排选项都应该是15u，decal(透明)键可以用来为HHKB这样的布局垫底。
* 最终的布局可能看起来如下：<sup>[(示例)](http://www.keyboard-layout-editor.com/#/gists/a93f0e6f320439e4e1d678cb04ac9af6)</sup> ![](../img/layout-options-final.png)

### 下载JSON文件

布局完成之后，进入KLE的"Raw data"(纯数据)标签，点击右下角的”Download JSON"(下载JSON)按钮。在本例中，输出开始是这样的。

```
[
  [
    "0,0",
    "0,1",
    "0,2",
    "0,3",
    "0,4",
    "0,5",
    "0,6",
    "0,7",
```

## 2\.创建一个包含完整键盘定义的JSON

从下面的JSON模板开始。

```json
{
    "lighting": "none",
    "matrix": {
        "rows": 0,
        "cols": 0
    },
    "layouts": {
        "labels":
        "keymap":
    }
}
```

填入所有的字段。

* `matrix`
  * `rows`: 输入键盘的行数，这个值应该与`config.h`中的`MATRIX_ROWS`设置相匹配。
  * `cols`: 类似地，这个值应该与键盘的`MATRIX_COLS`相匹配。
* `layouts`
  * `labels`: 上面描述的布局选项数组在这里；如果键盘没有任何布局选项，那么应该删除这一行。如果键盘有布局选项，请确保用逗号来结束这一行。
  * `keymap`：在冒号之后粘贴上一步下载的KLE JSON的全部内容。
* `name`,`vendorId`,`productId`: 这些选项是VIA所要求的，Vial不需要它们。你不需要添加它们，即使你添加了，它们的内容也不会被使用。

请参考[这里](https://github.com/vial-kb/vial-qmk/blob/12950db4d8ec1f294b1285e9b554a8fdc0a4bc6d/keyboards/idb/idb_60/keymaps/vial/vial.json)，是一个有多种布局选项的键盘定义JSON的例子。

## 完成

这里得到一个基本的键盘定义JSON文件。下一步，开始[建立Vial支持](/porting-to-vial.md)。

你遇到了问题吗？你可以通过加入[Vial discord](https://discord.gg/zNKEUXTKwF)获得移植支持。