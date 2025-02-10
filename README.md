# 推荐阅读
- 语雀文档 https://www.yuque.com/lwmacct/macos/karabiner
- 下载安装 https://karabiner-elements.pqrs.org/

# 使用示例
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
