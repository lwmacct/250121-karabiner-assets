# 架构说明

## 多层状态机设计

本配置基于 CapsLock 键实现了一个多层状态机系统，使用单一变量 `lwm_caps_lock` 管理所有状态：

### 状态定义

| lwm_caps_lock | 状态   | 触发条件                                      | 按键行为                             |
| ------------- | ------ | --------------------------------------------- | ------------------------------------ |
| 0             | 普通   | 默认                                          | 正常输入                             |
| 1             | 第一层 | CapsLock 按住                                 | 现有绑定生效（桌面切换、Tab 切换等） |
| 2             | 第二层 | 双击 CapsLock 按住 / 第一层 + Space | tmux 快捷键                          |
| 3+            | 可扩展 | 未来可加更多层                                | ...                                  |

### 核心文件

- **lwm-cl-caps_lock.json** - 核心配置：
  - CapsLock 单击按住 → `lwm_caps_lock = 1`（进入第一层）
  - CapsLock 松开 → `lwm_caps_lock = 0`（退出所有层）
  - 300ms 内双击 CapsLock 并按住 → `lwm_caps_lock = 2`（进入第二层）
  - 第一层 + Space → `lwm_caps_lock = 2`（进入第二层，松开 CapsLock 退出）
  - 使用 `lwm_double_tap_window` 变量实现双击检测

### 双击检测原理

```
第一次按下 CapsLock:
├── 进入第一层 (lwm_caps_lock = 1)
├── 开启双击窗口 (lwm_double_tap_window = 1)
└── 松开后 300ms 自动关闭双击窗口

第二次按下 CapsLock (在 300ms 内):
├── 检测到 lwm_double_tap_window = 1
├── 进入第二层 (lwm_caps_lock = 2)
└── 松开后关闭双击窗口
```

### 双击窗口关闭条件

| 条件                | 说明                   |
| ------------------- | ---------------------- |
| 300ms 超时          | 自动关闭，防止意外触发 |
| 第二层松开 CapsLock | 退出第二层时关闭       |

---

## 按键速查表

### 第一层 (CapsLock 按住)

#### 光标定位 (lwm-cl-cursor.json)

| 按键 | 功能       | 等效按键  |
| ---- | ---------- | --------- |
| i    | 方向键上   | ↑         |
| k    | 方向键下   | ↓         |
| j    | 方向键左   | ←         |
| l    | 方向键右   | →         |
| h    | 移动到行首 | Cmd + ←   |
| n    | 移动到行尾 | Cmd + →   |
| u    | 向左选中   | Shift + ← |
| o    | 向右选中   | Shift + → |

#### 桌面切换 (lwm-cl-desktop.json)

| 按键 | 功能    | 等效按键          |
| ---- | ------- | ----------------- |
| q    | 桌面 1  | Ctrl + 1          |
| w    | 桌面 2  | Ctrl + 2          |
| e    | 桌面 3  | Ctrl + 3          |
| r    | 桌面 4  | Ctrl + 4          |
| a    | 桌面 5  | Ctrl + 5          |
| s    | 桌面 6  | Ctrl + 6          |
| d    | 桌面 7  | Ctrl + 7          |
| f    | 桌面 8  | Ctrl + 8          |
| z    | 桌面 9  | Ctrl + 9          |
| x    | 桌面 10 | Ctrl + 0          |
| c    | 桌面 11 | Ctrl + Option + 1 |
| v    | 桌面 12 | Ctrl + Option + 2 |

#### Tab 切换 (lwm-cl-tab_toggle.json)

| 按键 | 功能       | 等效按键           |
| ---- | ---------- | ------------------ |
| .    | 下一个 Tab | Ctrl + Tab         |
| ,    | 上一个 Tab | Ctrl + Shift + Tab |

#### 窗口磁贴 (lwm-cl-magnet.json)

| 按键 | 功能                    | 等效按键                                  |
| ---- | ----------------------- | ----------------------------------------- |
| m    | 最大化窗口 / 自定义窗口 | Ctrl + Option + Enter / Ctrl + Option + M |

### 第二层 (双击 CapsLock 按住 / 第一层 + Space)

#### tmux 操作 (lwm-cl-tmux.json)

**窗口管理**

| 按键 | 功能       | 等效按键   |
| ---- | ---------- | ---------- |
| c    | 新建窗口   | Ctrl+B c   |
| n    | 下一个窗口 | Ctrl+B n   |
| p    | 上一个窗口 | Ctrl+B p   |
| w    | 窗口列表   | Ctrl+B w   |
| l    | 上次窗口   | Ctrl+B l   |
| 0-9  | 切换窗口   | Ctrl+B 0-9 |

**窗格管理**

| 按键 | 功能       | 等效按键 |
| ---- | ---------- | -------- |
| v    | 垂直分割   | Ctrl+B % |
| s    | 水平分割   | Ctrl+B " |
| o    | 切换窗格   | Ctrl+B o |
| x    | 关闭窗格   | Ctrl+B x |
| z    | 最大化窗格 | Ctrl+B z |

**其他**

| 按键 | 功能     | 等效按键 |
| ---- | -------- | -------- |
| d    | 分离会话 | Ctrl+B d |
| [    | 复制模式 | Ctrl+B [ |
| ]    | 粘贴     | Ctrl+B ] |
| ;    | 命令模式 | Ctrl+B : |
| /    | 帮助     | Ctrl+B ? |

---

# 推荐阅读

- 代码仓库 [https://github.com/lwmacct/250121-karabiner-assets](https://github.com/lwmacct/250121-karabiner-assets)
- 语雀文档 [https://www.yuque.com/lwmacct/macos/karabiner](https://www.yuque.com/lwmacct/macos/karabiner)
- 下载安装 [https://karabiner-elements.pqrs.org/](https://karabiner-elements.pqrs.org/)
- win 平替 [https://github.com/moshuying/myAHK](https://github.com/moshuying/myAHK)

# 使用示例

- macos 打开终端直接执行 (需要先安装 git)

```bash
#!/usr/bin/env bash

__main() {
  cd ~/.config/karabiner || return 1
  rm -rf assets
  git clone https://github.com/lwmacct/250121-karabiner-assets.git assets
}

__main
```

---

# 语雀贴图

![](https://cdn.nlark.com/yuque/0/2025/png/780867/1737446037507-1e915634-9187-457b-8071-4c6f7df00c62.png)

![](https://cdn.nlark.com/yuque/0/2025/png/780867/1737447084068-8f3a00ea-c956-42e3-9b70-f43066e27a25.png)

![](https://cdn.nlark.com/yuque/0/2025/png/780867/1737448228174-70e2ab97-ef99-4a68-b068-5ababaf78e1f.png)
