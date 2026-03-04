# 架构说明

## 多层状态机设计

本配置基于 CapsLock 键实现了一个多层状态机系统，使用单一变量 `lwm_caps_lock` 管理所有状态：

### 状态定义

| lwm_caps_lock | 状态   | 触发条件              | 按键行为                             |
| ------------- | ------ | --------------------- | ------------------------------------ |
| 0             | 普通   | 默认                  | 正常输入                             |
| 1             | 第一层 | CapsLock 切换进入     | 光标、桌面、Tab 切换等               |
| 2             | 第二层 | 第一层 + Space        | 预留模式（a-z 待定义）               |

### 核心文件

- **m-core-caps-state.json** - 核心配置：
  - CapsLock 在 `0/1/2` 间实现「进入第一层 / 退出全部层」切换
  - 第一层 + Space → `lwm_caps_lock = 2`（进入第二层/预留模式）
  - Esc（在任意层）→ `lwm_caps_lock = 0`（退出全部层）
- **m-l1-navigation.json** - 第一层导航映射（光标/Tab/窗口磁贴）
- **m-l2-mode.json** - 第二层预留映射（a-z）
- **m-map-input-toggle.json** - 输入法切换映射：
  - Right Command 单击（按下后立即松开）→ `Ctrl + Space`

### 模式切换流程

```
按下 CapsLock:
├── 0 -> 1（进入第一层）
└── 1/2 -> 0（退出全部层）

第一层中:
└── Space -> 2（进入第二层预留模式）

任意层中:
└── Esc -> 0（退出全部层）
```

---

## 按键速查表

### 第一层 (CapsLock 切换进入)

#### 光标定位 (m-l1-navigation.json)

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

#### 桌面切换 (m-l1-desktop.json)

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

#### Tab 切换 (m-l1-navigation.json)

| 按键 | 功能       | 等效按键           |
| ---- | ---------- | ------------------ |
| .    | 下一个 Tab | Ctrl + Tab         |
| ,    | 上一个 Tab | Ctrl + Shift + Tab |

#### 窗口磁贴 (m-l1-navigation.json)

| 按键 | 功能                    | 等效按键                                  |
| ---- | ----------------------- | ----------------------------------------- |
| m    | 最大化窗口 / 自定义窗口 | Ctrl + Option + Enter / Ctrl + Option + M |

### 第二层 (第一层 + Space)

#### 预留模式 (m-l2-mode.json)

| 按键 | 当前行为 |
| ---- | -------- |
| a-z  | 占位拦截（`vk_none`） |

### 其他映射 (m-map-input-toggle.json)

| 按键               | 功能       | 等效按键     |
| ------------------ | ---------- | ------------ |
| Right Command 单击 | 输入法切换 | Ctrl + Space |

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
