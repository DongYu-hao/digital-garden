## **文档目的**

本指南提供了一个标准化的、可重复使用的解决方案，旨在帮助 macOS 用户通过一个简单的命令行脚本，将任何指定的应用程序设置为多种文件类型的默认打开方式。无论您是想将所有代码文件默认用 VS Code 打开，还是将所有 Markdown 文件用 Typora 打开，本指南都能为您提供清晰的步骤。

## **核心概念：为什么使用脚本？**

macOS 内部使用一种名为 **统一类型标识符 (Uniform Type Identifier, UTI)** 的机制来管理文件和应用之间的关联，而非简单地依赖文件后缀名。手动通过“显示简介”面板逐一修改，不仅效率低下，而且容易遗漏。

使用命令行工具 `duti`，我们可以直接与这个底层系统交互，实现精确、快速的批量操作。

**使用脚本的优势：**
*   **高效：** 一次执行，即可设定数十种文件类型的关联。
*   **可重复：** 脚本保存后，在新电脑上或系统重装后可一键恢复设置。
*   **精确：** 直接操作 UTI 关联，比图形界面更可靠。
*   **易于分享：** 可以轻松将您的配置脚本分享给同事或朋友。

---

## **第一部分：核心方法 —— `duti` 工具**

这是完成此任务的**最佳实践**。

### **步骤一：环境准备 (仅需一次)**

1.  **安装 Homebrew (macOS 包管理器)**
    如果您的系统中尚未安装，请打开“终端” (Terminal) 应用，并执行以下命令：
    ```bash
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```
    根据提示输入您的密码以完成安装。

2.  **安装 `duti`**
    通过 Homebrew 安装 `duti`：
    ```bash
    brew install duti
    ```

### **步骤二：获取目标应用的 Bundle ID**

要将文件关联到某个应用，我们必须知道它的“身份证号”——即 Bundle ID。

**通用获取命令：**
打开终端，使用以下命令格式，将 `[应用名称]` 替换为您想要设置的应用名（通常是它在“应用程序”文件夹中的名字）。

```bash
osascript -e 'id of app "[应用名称]"'
```

**常见示例：**
*   **Visual Studio Code:**
    ```bash
    osascript -e 'id of app "Visual Studio Code"'
    # 返回: com.microsoft.VSCode
    ```
*   **Sublime Text:**
    ```bash
    osascript -e 'id of app "Sublime Text"'
    # 返回: com.sublimetext.4 (版本号可能不同)
    ```
*   **Zed:**
    ```bash
    osascript -e 'id of app "Zed"'
    # 返回: dev.zed.Zed
    ```
*   **IINA (视频播放器):**
    ```bash
    osascript -e 'id of app "IINA"'
    # 返回: com.colliderli.iina
    ```

请记下您获取到的 Bundle ID，下一步脚本中会用到。

### **步骤三：创建并自定义通用脚本**

1.  **创建脚本文件**
    在您喜欢的位置（如桌面）创建一个名为 `set_defaults.sh` 的文件。

2.  **粘贴通用模板**
    将下面的通用脚本模板完整复制到文件中。这是一个经过精心设计和注释的模板，您只需修改少量变量即可使用。

    ```bash
    #!/bin/bash
    
    # ==============================================================================
    # macOS 通用文件关联设置脚本
    #
    # 使用方法:
    # 1. 修改下面的 `APP_NAME` 和 `APP_BUNDLE_ID` 变量。
    # 2. 在 `EXTENSIONS` 列表中自定义您需要关联的文件后缀。
    # 3. 终端中运行: `chmod +x set_defaults.sh && ./set_defaults.sh`
    # ==============================================================================
    
    # --- 1. 请修改这里 ---
    # 您想要设置为默认的应用名称（用于日志输出，方便辨认）
    APP_NAME="Visual Studio Code"
    # 该应用的 Bundle ID (通过 `osascript -e 'id of app "应用名"'` 获取)
    APP_BUNDLE_ID="com.microsoft.VSCode"
    
    
    # --- 2. 在此自定义您需要关联的文件后缀名列表 ---
    EXTENSIONS=(
        # 通用编程与脚本
        "py" "js" "ts" "jsx" "tsx" "rb" "sh" "bash" "zsh" "go" "rs" "java"
        "kt" "swift" "cpp" "c" "h" "hpp" "cs" "php" "pl" "perl" "lua" "r" "dart"
    
        # 配置文件
        "xml" "yml" "yaml" "json" "toml" "ini" "conf" "cfg" "env" "properties"
    
        # 特殊文件名 (duti 也能很好地处理)
        ".env" "Dockerfile" "Makefile"
    
        # Web 开发
        "html" "htm" "css" "scss" "sass" "less" "vue" "svelte"
    
        # 文档与数据格式
        "md" "markdown" "sql" "csv" "log" "txt" "text" "jsonl"
    
        # 构建与包管理
        "gradle" "tf" "hcl" "mod" "sum" "cabal" "lock" "pom.xml" "build.sbt"
    )
    
    # --- 脚本执行区 (通常无需修改) ---
    echo "准备为 '$APP_NAME' 设置默认关联..."
    echo "目标应用 Bundle ID: $APP_BUNDLE_ID"
    echo "将要处理 ${#EXTENSIONS[@]} 种文件类型。"
    echo "----------------------------------------------------"
    
    # 检查 duti 是否已安装
    if ! command -v duti &> /dev/null; then
        echo "错误: duti 命令未找到。"
        echo "请先通过 Homebrew 安装: brew install duti"
        exit 1
    fi
    
    # 循环遍历列表，为每个后缀名设置默认应用
    for EXT in "${EXTENSIONS[@]}"; do
      echo "正在关联: $EXT -> $APP_NAME"
      # 使用 duti 命令进行设置
      # `duti -s [bundle_id] [extension] [role]`
      # role 'all' 表示同时设置编辑器、查看器等所有角色
      duti -s "$APP_BUNDLE_ID" "$EXT" all
    done
    
    echo "----------------------------------------------------"
    echo "✅ 全部设置完成！"
    echo "macOS 可能需要一些时间来更新文件图标。如果未立即生效，可以尝试重启 Finder。"
    
    ```

### **步骤四：执行脚本**

1.  **授予执行权限**
    打开“终端”，使用 `cd` 命令进入脚本所在目录，然后运行：
    ```bash
    chmod +x set_defaults.sh
    ```

2.  **运行脚本**
    ```bash
    ./set_defaults.sh
    ```
    脚本将开始运行，并清晰地打印出每一步操作。

---

## **第二部分：验证与故障排除**

### **如何验证设置？**

1.  在 Finder 中找到一个您已设置过的文件类型（如 `.md` 文件）。
2.  右键点击文件，选择 **“显示简介”** (Get Info)。
3.  在弹出的窗口中，找到 **“打开方式”** (Open with) 一栏，确认默认程序是否已变为您设定的应用。
4.  （可选）点击 **“全部更改…”** (Change All...) 按钮，可以加强该类型所有文件的关联。

### **未立即生效怎么办？**

macOS 有时会缓存文件图标和关联信息。如果设置后没有立刻看到变化，请尝试以下操作：
*   **重启 Finder:** 按住 `Option` 键，右键点击 Dock 上的 Finder 图标，选择“重新开启”(Relaunch)。
*   **重启电脑:** 这是最彻底的刷新方式。

---

## **附录：传统手动方法 (效率较低)**

作为对比，这里也列出 macOS 原生的手动设置方法，以便您理解脚本所带来的便利。

1.  在 Finder 中右键单击任意一个您想更改的文件（例如 `example.md`）。
2.  选择“显示简介”。
3.  在“打开方式”部分，从下拉菜单中选择您想要的应用程序。
4.  点击下方的“全部更改…”按钮。
5.  系统会弹出确认框，询问您是否要将所有类似文稿的打开方式都更改为该程序，点击“继续”。

**缺点：** 您需要为**每一种**文件后缀重复以上所有步骤，非常耗时。