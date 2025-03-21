# 🧠 Stable Diffusion WebUI Dockerized (Forge / AUTOMATIC1111)

本项目通过 Docker 打包 `Stable Diffusion WebUI`，支持版本包括 `Forge` 和 `AUTOMATIC1111`。项目内置了模型管理、插件自动安装、FFmpeg 支持以及环境变量配置等高级特性。

> ✅ 支持 `SD 1.5`, `SDXL`, `FLUX` 等模型  
> ✅ 可选 HuggingFace 与 Civitai Token  
> ✅ 自动安装扩展与模型  
> ✅ 支持自定义参数启动

---

## 📦 项目结构

```plaintext
your-project/
├── Dockerfile              # 构建镜像
├── run.sh                  # 启动逻辑（初始拉取、venv创建、插件和模型下载等）
├── resources.txt           # 插件与模型的自动下载配置
├── ffmpeg                  # 内置可执行 ffmpeg（可选）
└── README.md               # 当前文件
```

---

### 2️⃣ 启动容器（推荐使用 forge UI）

运行以下命令以启动容器，支持图形处理加速和多种自定义选项：

```bash
docker run --gpus all -it --rm \
  -p 7860:7860 \
  -v $(pwd)/outputs:/home/webui/outputs \
  -v $(pwd)/models:/home/webui/models \
  -e UI=forge \
  -e ARGS="--xformers --api --listen --theme dark" \
  -e HUGGINGFACE_TOKEN=your_hf_token_here \
  -e CIVITAI_API_TOKEN=your_civitai_token_here \
  sd-webui
```

☝️ Token 环境变量为可选项，如果不提供，系统将自动跳过相关登录。

----

⚙️ **参数说明**

| 参数                | 说明                                                                         |
|---------------------|------------------------------------------------------------------------------|
| `UI`                | 可选值：`forge`（默认） 或 `auto`（AUTOMATIC1111）                            |
| `ARGS`              | 启动 `webui.sh` 时的额外参数，例如 `--api --listen --xformers`               |
| `HUGGINGFACE_TOKEN` | 可选，自动登录 HuggingFace CLI（用于下载私有模型）                            |
| `CIVITAI_API_TOKEN` | 可选，用于扩展访问 Civitai 接口（部分插件使用）                               |

🔌 **插件与模型自动管理**

项目中提供了 `resources.txt` 文件，你可以编辑它来自定义各类插件和模型的下载路径，如下所示：

```plaintext
extensions/sd-webui-wd14-tagger,https://github.com/Akegarasu/sd-webui-wd14-tagger.git
models/ControlNet/control_v11p_sd15_canny.pth,https://huggingface.co/.../canny.pth
models/VAE/flux-ae.safetensors,https://huggingface.co/.../flux-ae.safetensors
```

🧠 **支持的模型类型（默认集成）**

- ✅ 标准调 SD1.5 主模型 + ControlNet v1.1 支持
- ✅ SDXL 兼容 ControlNet 多模型结构
- ✅ 与 FLUX.1 专用模型，特定 VAE 及文本编码模型配合使用

📂 **输出与模型目录挂载建议**

为了实现文件持久化，建议把以下路径挂载至宿主机：

| 容器路径             | 建议挂载   | 说明                                    |
|----------------------|------------|-----------------------------------------|
| `/home/webui/outputs` | `./outputs` | 用于保存生成的图片等输出内容            |
| `/home/webui/models`  | `./models`  | 模型存储目录，容器会自动按照需求创建结构  |

🛠️ **常见问题**

- ❓ **为什么会看到 TCMalloc 报错？**
  - 默认设置中，TCMalloc 已禁用（`NO_TCMALLOC=1`）。如需修改此设置，应确保系统支持。

- ❓ **为什么插件更新失败？**
  - 请验证网络正常，且确认 `resources.txt` 中设定的插件路径为 Git 仓库。非 Git 项目将自动提示跳过更新。

---
