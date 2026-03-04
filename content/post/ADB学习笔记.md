+++
date = '2026-01-24T18:22:33+08:00'
draft = false
title = 'ADB学习笔记'
+++
# Android ADB 学习与实战笔记

---

## 一、 基础概念

### 1. 什么是 ADB?
* **全称**：Android Debug Bridge (安卓调试桥)
* **作用**：用于电脑（Windows/Mac/Linux）与安卓设备（手机/模拟器）之间进行通信和控制的命令行工具。
* **底层原理**：安卓系统是基于 **Linux 内核** 开发的，因此很多操作逻辑与 Linux 相同。



#### 对比知识点：
| 平台 | 调试工具/库 |
| :--- | :--- |
| **Android** | 使用 **ADB** 进行通信调试 |
| **iOS** | 使用 **Xcode** (IDE) 或 **libimobiledevice** 库进行通信调试 |

### 2. 环境变量配置
为了在电脑的任何目录下都能使用 `adb` 命令，需要将 `adb.exe` 所在的路径加入环境变量。
* **用户变量**：仅对当前登录的电脑用户生效（优先级高于系统变量）。
* **系统变量**：对所有登录该电脑的用户生效。
* **配置目的**：让 Windows 的命令提示符（CMD）知道去哪里找到 `adb.exe` 这个可执行文件。

---

## 二、 常用 ADB 指令

### 1. 设备连接与状态
* `adb devices`：查看当前连接的设备列表。
* `adb connect [IP]:[端口]`：通过无线 WiFi 连接手机。
* `adb disconnect`：断开无线连接。
* `adb kill-server` / `adb start-server`：重启 ADB 服务（连接异常时使用）。

### 2. Shell 模式
* `adb shell`：进入安卓手机的 Linux 系统命令行模式。
    * **提示符**：`$` 代表普通用户，`#` 代表 Root（管理员）用户。
* `exit`：退出安卓 Shell，回到 Windows 命令行。

### 3. 应用管理 (安装/卸载)
* **安装 APK**：`adb install [电脑上的apk路径]`
    * *例如：`adb install D:\android_apk\litiaotiao.apk`*
    * *覆盖安装（保留数据）：`adb install -r [apk路径]`*
* **卸载应用**：`adb uninstall [应用包名]`
    * *例如：`adb uninstall com.litiaotiao.realfriends`*
    * *卸载但保留数据：`adb uninstall -k [应用包名]`*

### 4. 文件传输 (电脑 <-> 手机)
* **从手机下载文件 (Pull)**：`adb pull [手机文件路径] [电脑存储路径]`
    * *例如：`adb pull /sdcard/DCIM/Camera/photo.jpg D:\Photos\`*
* **上传文件到手机 (Push)**：`adb push [电脑文件路径] [手机存储路径]`
    * *例如：`adb push D:\test.txt /sdcard/Download/`*

---

## 三、 安卓系统内部指令 (Linux Shell)

这些指令通常在输入 `adb shell` 后使用，或者写作 `adb shell [指令]`。

| 指令 | 英文全称 | 作用 | 备注 |
| :--- | :--- | :--- | :--- |
| **ls** | List | 列出当前目录下的内容 | 类似 Windows 的 `dir` |
| **ls -l** | List Long | 以长格式列出详情 | 常用别名 `ll` (部分系统可用) |
| **cd [目录名]** | Change Directory | 进入指定的文件夹 | ❌ 不能进入文件 |
| **cd ..** | Parent Directory | 返回上一级目录 | 注意中间有空格 |
| **pwd** | Print Working Dir | 显示当前所在的完整路径 | 防止迷路 |
| **cat [文件名]** | Concatenate | 查看文件的文本内容 | 相当于打开记事本查看 |
| **rm [文件名]** | Remove | 永久删除文件 | ⚠️ 谨慎使用，无回收站 |
| **rm -r [目录]** | Remove Recursive | 删除文件夹及其包含的所有内容 | 删除文件夹必用 |
| **mkdir [目录名]** | Make Directory | 创建一个新的文件夹 | —— |

---

## 四、 高级管理命令

### 1. 包管理 (PM - Package Manager)
实际上是调用 `/system/bin/pm` 脚本。
* `adb shell pm list packages`：列出手机内所有应用的包名。
* `adb shell pm list packages -3`：列出手机内第三方（用户安装）应用的包名。
* `adb shell pm list packages -s`：列出手机内系统应用的包名。
* `adb shell pm clear [包名]`：清除指定应用的数据和缓存（相当于“恢复出厂设置”）。

### 2. 页面/活动管理 (AM - Activity Manage)
手机的每一个界面都是一个 **Activity**。
* **启动应用**：`adb shell am start -n [包名]/[启动Activity路径]`
    * *例如启动浏览器访问网页：*
      `adb shell am start -a android.intent.action.VIEW -d "http://www.baidu.com"`
* **获取当前页面信息**：
    *(用于查看当前正在前台显示的 App 包名和 Activity 名)*
    ```bash
    adb shell dumpsys window | grep mCurrentFocus
    # 或者 (更通用的方式)
    adb shell "dumpsys activity activities | grep mResumedActivity"
    ```

### 3. 自动化输入 (Input)
模拟用户的手指和按键操作。



* **输入文本**：`adb shell input text "HelloWorld"` (不支持直接输入中文)
* **点击坐标**：`adb shell input tap [x坐标] [y坐标]`
* **滑动屏幕**：`adb shell input swipe [x1] [y1] [x2] [y2] [耗时ms]`
* **模拟按键 (Keyevent)**：`adb shell input keyevent [KeyCode]`
    * **常用 KeyCode 速查**：
        * `3`: Home 键 (主页)
        * `4`: Back 键 (返回)
        * `26`: Power 键 (电源)
        * `66`: Enter 键 (回车)
        * `24`: 音量 +
        * `25`: 音量 -

---

## 五、 学习心得与易错点

> ### 💡 经验总结
> 1.  **文件 vs 目录**：`cd` 只能进目录（文件夹），`cat` 只能看文件（文本）。
> 2.  **只读系统**：`/system` 目录通常是只读的 (Read-only)，没有 Root 权限无法修改其中的文件。普通文件建议放在 `/sdcard/` 下。
> 3.  **字符混淆**：
>     * `l`(小写L) 和 `1`(数字1) 容易看错，`ls` 是小写L。
>     * **空格**非常重要，如 `cd ..` 中间必须有空格。