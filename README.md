# Karabiner 映射说明（按当前代码）

本仓库只包含 `complex_modifications/*.json`，对应 5 组规则：

- `m-core-caps-state.json`：层状态机（Caps/L1/L2）
- `m-l1-navigation.json`：L1 导航、Tab 切换、窗口磁贴
- `m-l1-desktop.json`：L1 桌面 1-12 切换
- `m-l2-mode.json`：L2 预留（a-z 全拦截）
- `m-keymap.json`：Left Shift 单击切输入法

## 快速使用

1. 安装 Karabiner-Elements。
2. 把本仓库里的 `complex_modifications/*.json` 放到：
   - `~/.config/karabiner/assets/complex_modifications/`
3. 打开 Karabiner-Elements -> Complex Modifications -> Add predefined rule。
4. 按需启用本仓库规则（当前共 9 条，按文件分组如下）：
   - `m-core-caps-state.json`：1 条
   - `m-l1-navigation.json`：5 条
   - `m-l1-desktop.json`：1 条
   - `m-l2-mode.json`：1 条
   - `m-keymap.json`：1 条

## 使用示例（Git 拉取脚本）

下面是用于在本机覆盖更新 `~/.config/karabiner/assets` 的脚本：

```bash
#!/usr/bin/env bash

__main() {
  cd ~/.config/karabiner || return 1
  rm -rf assets
  git clone https://github.com/lwmacct/250121-karabiner-assets.git assets
}

__main
```

## 状态机

使用 3 个变量：

- `lwm_caps_lock`：是否进入第一层（L1）
- `lwm_l2_hold`：是否进入第二层（L2）
- `lwm_cl_key_m`：L1 下 `m` 键二段切换状态

状态切换逻辑：

1. 按住 `CapsLock`（阈值 1ms）-> `lwm_caps_lock=1`，进入 L1
2. 在 L1 按住 `Space` -> `lwm_l2_hold=1`，进入 L2
3. 松开 `Space` -> `lwm_l2_hold=0`，回到 L1
4. 松开 `CapsLock` -> `lwm_caps_lock=0`、`lwm_l2_hold=0`、`lwm_cl_key_m=0`

状态含义：

| 条件 | 状态 |
| --- | --- |
| `lwm_caps_lock=0` | 普通层（L0） |
| `lwm_caps_lock=1` 且 `lwm_l2_hold=0` | 第一层（L1） |
| `lwm_caps_lock=1` 且 `lwm_l2_hold=1` | 第二层（L2） |

## 按键速查表

### 普通层（`lwm_caps_lock=0`）

| 按键 | 条件 | 输出 |
| --- | --- | --- |
| Left Shift 单击 | 180ms 内单独按下并抬起 | `Ctrl + Space` |
| Left Shift 长按/组合 | 与其他键组合或超过单击超时 | 保持 `Shift` 原行为 |

> 说明：Left Shift 输入法切换只在普通层生效（L1/L2 不生效）。

### 第一层 L1（按住 Caps，且 `lwm_l2_hold=0`）

#### 光标与选中

| 按键 | 输出 |
| --- | --- |
| `i` | `Up`（可连发） |
| `k` | `Down`（可连发） |
| `j` | `Left`（可连发） |
| `l` | `Right`（可连发） |
| `h` | `Cmd + Left` |
| `n` | `Cmd + Right` |
| `u` | `Shift + Left`（可连发） |
| `o` | `Shift + Right`（可连发） |

#### Tab 切换

| 按键 | 输出 |
| --- | --- |
| `.` | `Ctrl + Tab` |
| `,` | `Ctrl + Shift + Tab` |

#### 桌面切换

| 按键 | 输出 |
| --- | --- |
| `q`/`w`/`e`/`r` | `Ctrl + 1/2/3/4` |
| `a`/`s`/`d`/`f` | `Ctrl + 5/6/7/8` |
| `z`/`x` | `Ctrl + 9/0` |
| `c`/`v` | `Ctrl + Option + 1/2` |

#### 窗口磁贴（`m` 二段切换）

| 连续按键（均在 L1） | 输出 | 变量变化 |
| --- | --- | --- |
| 第 1 次按 `m` | `Ctrl + Option + Enter` | `lwm_cl_key_m: 0 -> 1` |
| 第 2 次按 `m` | `Ctrl + Option + m` | `lwm_cl_key_m: 1 -> 0` |

### 第二层 L2（按住 Caps+Space，`lwm_l2_hold=1`）

| 按键 | 输出 |
| --- | --- |
| `a-z` | `vk_none`（占位拦截，可连发） |

> 说明：L2 目前仅拦截字母键 `a-z`，其余未定义键位保持系统默认行为。

## 代码级注意事项

- `CapsLock` 在这套规则里是层切换键，不再发送原始 `caps_lock`。
- `CapsLock` 单击不会触发任何输出（没有 `to_if_alone`）。
- 在 L1 按住 `Space` 会切到 L2，`Space` 本身不会输出空格。
- `lwm_cl_key_m` 会在按下/松开 `CapsLock` 时清零，避免跨次残留。

## 自检清单（按当前代码）

1. 开启 EventViewer，确认按住 `CapsLock` 时变量 `lwm_caps_lock=1`。
2. 在按住 `CapsLock` 的同时按住 `Space`，确认 `lwm_l2_hold=1`。
3. L1 按 `i/j/k/l`，应输出方向键；L2 按 `a-z` 应无输出（`vk_none`）。
4. 普通层单击 Left Shift（小于 180ms）应输出 `Ctrl+Space`。
