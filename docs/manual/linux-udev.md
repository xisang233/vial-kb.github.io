---


layout: default title: Configuring udev on Linux parent: User manual nav\_order: 6 has\_toc: true redirect\_from:

- /getting-started/linux-udev.html

---


# 为Linux上的VIA和Vial配置`udev`规则

为了让你的键盘被Linux上的VIA和Vial应用程序检测到，你需要设置一个自定义`udev`规则。两者都使用`hidraw`linux驱动来直接与你的键盘进行配置通信。在大多数情况下，对所有`hidraw`设备使用通用`udev`规则应该是安全的，但在其他情况下，如果你喜欢或需要在设备基础上限制访问，可以[为你的键盘专门创建一个](#device-specific-udev-rules)`udev`规则。

Vial有一个插入USB串行属性的神奇数字，所以你只需要为所有Vial键盘制定一个规则，与其他`hidraw`设备规则一起使用会很安全。

下面的指南将告诉你如何实现这些`udev`规则。你将需要root权限来创建必要的文件。

## 通用Vial`udev`规则

对于任何带有Vial固件的设备的通用访问规则。在你的shell中以你的用户身份登录时运行这个命令（仅在安装了`sudo`的系统下可用）：

```
export USER_GID=`id -g`; sudo --preserve-env=USER_GID sh -c 'echo "KERNEL==\"hidraw*\", SUBSYSTEM==\"hidraw\", ATTRS{serial}==\"*vial:f64c2b3c*\", MODE=\"0660\", GROUP=\"$USER_GID\", TAG+=\"uaccess\", TAG+=\"udev-acl\"" > /etc/udev/rules.d/99-vial.rules && udevadm control --reload && udevadm trigger'
```

该命令将自动创建一个`udev`规则并重新加载`udev`系统。

### 手动操作

使用文本编辑器打开 `/etc/udev/rules.d/99-vial.rules` 并加入下列内容：

```
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{serial}=="*vial:f64c2b3c*", MODE="0660", GROUP="users", TAG+="uaccess", TAG+="udev-acl"
```

你可以在你的操作系统上用任何你需要的组合来代替，但`users`组应当在大多数Linux发行版上可用。

为了使规则生效，你必须[重新加载`udev`](#reloading-udev).

## 通用的VIA`udev`规则

在内核层面上，没有办法检测键盘是否与VIA兼容，所以你必须允许对所有设备`hidraw`的访问。如果你希望硬件更加安全，你可以设置并[允许特定设备访问](#device-specific-udev-rules)。

对于允许用户访问所有`hidraw`设备的规则，在你的shell中以你的用户身份登录时运行这个命令（仅在安装了 `sudo` 的系统下可用）：

```
export USER_GID=`id -g`; sudo --preserve-env=USER_GID sh -c 'echo "KERNEL==\"hidraw*\", SUBSYSTEM==\"hidraw\", MODE=\"0660\", GROUP=\"$USER_GID\", TAG+=\"uaccess\", TAG+=\"udev-acl\"" > /etc/udev/rules.d/92-viia.rules && udevadm control --reload && udevadm trigger' 
```

该命令将自动创建一个`udev`规则并重新加载`udev`系统。

### 手动操作

使用文本编辑器打开`/etc/udev/rules.d/92-viia.rules`并加入下列内容：

```
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="users", TAG+="uaccess", TAG+="udev-acl"
```

你可以在你的操作系统上用任何你需要的组合来代替，但`users`组应当在大多数Linux发行版上可用。

为了使规则生效，你必须[重新加载`udev`](#reloading-udev).

## 特定设备的`udev`规则

### 寻找供应商和产品ID

配置特定设备`udev`规则需要你事先知道键盘的USB Vendor ID(供应商ID)和Product ID(产品ID)。找到它们的最简单方法是使用`lsusb`命令。大多数主要的Linux发行版预装`lsusb`，但你可能需要手动安装它。`lsusb`将列出你所有连接的USB设备的信息。下面是Keychron Q2 Revision 0110的输出示例。

```
Bus 001 Device 049: ID 3434:0110 Keychron Keychron Q2
```

`ID`旁边的前四个十六进制数字是供应商ID，冒号后面的四个十六进制数字是产品ID。在这种情况下，Keychron Q2的供应商ID是`3434`，它的产品ID是`0110`。

#### 我的键盘在`lsusb`的哪里？

部分键盘的设备名称可能会丢失，或者根本就不是你的键盘的名称。这是由于大多数QMK兼容键盘不是由在USBIF注册了供应商ID的大公司制造的。因此，ID有时被设置为任意的或非官方的值，或者使用微控制器的USB接口提供的ID。

要想知道哪个设备是你的键盘，在你的键盘插上电源的情况下运行`lsusb`，然后在断开电源的情况下再次运行。对比两次的输出，即可找到键盘。

### 创建`udev`规则

要为您的特定键盘创建一个规则，请使用此模板。

```
# Name of your keyboard
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="XXXX", ATTRS{idProduct}=="XXXX", MODE="0660", GROUP="users", TAG+="uaccess", TAG+="udev-acl"
```

找到的键盘的供应商和产品ID，用[lsusb](#finding-the-vendor-and-product-ids)替换。如果需要，你也可以指定一个不同的组。用root身份把这个规则写到`/etc/udev/rules.d/99-vial.rules`文件下。为了使规则生效，你必须[重新加载`udev`](#reloading-udev).

下面是Keychron Q2的一个`udev`规则示例。

```
# Keychron Q2
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="3434", ATTRS{idProduct}=="0110", MODE="0660", GROUP="users", TAG+="uaccess", TAG+="udev-acl"
```

## 重新加载`udev`

为了让你的新`udev`规则生效，你必须重新加载`udev`规则。这样做的标准方法是以root身份运行以下命令：

```
udevadm control --reload-rules && udevadm trigger
```

如果这个命令不起作用，或者它不可用，请检查你是否以root身份操作或使用了权限升级工具，如`sudo` ，并阅读你的键盘制造商的手册。