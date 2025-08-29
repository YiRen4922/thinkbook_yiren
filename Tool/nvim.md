
# keymap

| keymap       | description               | 描述              |
| ------------ | ------------------------- | --------------- |
| `<leader>sd` | search diagnostics        | 搜索lsp诊断         |
| `<leader>sD` | search buffer diagnostics | 搜索lsp诊断         |
| `<leader>sb` | search buffer line        | 搜索当前buffer      |
| `<leader>sB` | search open  buffers      | 搜索当前所有打开的buffer |
| `<leader>sg` |                           |                 |
| `<leader>sG` |                           |                 |
| `<leader>sk` | search keymaps            | 查找键位映射          |
|              |                           |                 |

# 开发嵌入式项目kernel内核
``` YAML
CompileFlags:
  # 1. 使用你指定的交叉编译器，让 clangd 能正确解析宏和头文件
  # 注意这里用的是 QueryDriver 而不是 Compiler，更通用
  QueryDriver:
    - /home/forlinx/work/OK3506_Linux_Source/prebuilts/gcc/linux-x86/arm/gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf/bin/arm-none-linux-gnueabihf-*

  # 2. 去掉内核里 clang 不认识的 GCC 专用参数
  Remove:
    - -mno-fdpic
    - -fno-allow-store-data-races
    - -fno-ipa-sra
    - -fconserve-stack

  # 3. 额外添加一些忽略报错的选项，避免干扰
  Add:
    - -Wno-unknown-warning-option
    - -Wno-unused-command-line-argument
    - -Wno-gnu-variable-sized-type-not-at-end
    - -Wno-address-of-packed-member

# 4. 索引设置
Index:
  Background: Build    # 后台持续索引内核源码
  StandardLibrary: Yes # 启用标准库索引（必要时）

# 5. clangd 行为优化
Diagnostics:
  Suppress: [pp_including_main_file] # 避免大量 #include 报错干扰

```