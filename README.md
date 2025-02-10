# 推荐阅读
- 代码仓库 https://github.com/lwmacct/250121-karabiner-assets
- 语雀文档 https://www.yuque.com/lwmacct/macos/karabiner
- 下载安装 https://karabiner-elements.pqrs.org/

# 使用示例
- macos 打开终端直接执行 (需要先安装 git)
```bash
#!/usr/bin/env bash

__main() {
  cd ~/.config/karabiner || return 1
  rm -rf assets
  git clone https://github.com/lwmacct/250121-karabiner-assets.git
  mv 250121-karabiner-assets assets
}

__main

```

