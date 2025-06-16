# Upload Docker Images

这个项目用于自动将Docker镜像推送到DockerHub。

## 功能特性

- 自动扫描`images`目录中的所有`.tar`格式的Docker镜像文件
- 通过GitHub Actions自动推送镜像到DockerHub
- 智能检测镜像是否已存在，避免重复推送
- 支持手动触发和代码变更自动触发

## 目录结构

```
upload-docker-images/
├── images/                    # 存放Docker镜像tar文件的目录
│   ├── ai-quota-shenma-0.0.1.tar
│   └── ... (其他镜像文件)
├── .github/
│   └── workflows/
│       └── push-images.yml    # GitHub Actions工作流
└── README.md
```

## 使用方法

### 1. 准备Docker镜像

将要推送的Docker镜像保存为tar文件，并放入`images`目录：

```bash
# 示例：保存镜像为tar文件
docker save -o images/ai-quota-shenma-0.0.1.tar zgsm/ai-quota-shenma:0.0.1
docker save -o images/your-image-name.tar your-registry/your-image:tag
```

### 2. 配置DockerHub凭据

在GitHub仓库设置中添加以下Secrets：

- `DOCKERHUB_USERNAME`: 你的DockerHub用户名
- `DOCKERHUB_TOKEN`: 你的DockerHub访问令牌

要创建DockerHub访问令牌：
1. 登录DockerHub
2. 进入Account Settings > Security
3. 点击"New Access Token"
4. 复制生成的令牌

### 3. 触发推送

有以下几种方式触发镜像推送：

#### 自动触发
- 当你向`main`分支推送包含`images`目录变更的代码时
- 当你创建针对`main`分支且包含`images`目录变更的Pull Request时

#### 手动触发
1. 进入GitHub仓库的Actions页面
2. 选择"Push Docker Images to DockerHub"工作流
3. 点击"Run workflow"按钮

### 4. 查看推送结果

在GitHub Actions页面可以查看推送过程和结果：
- 成功推送的镜像会显示"成功推送镜像"消息
- 已存在的镜像会显示"已存在于DockerHub，跳过推送"消息
- 推送失败会显示错误信息

## 工作流说明

GitHub Actions工作流会执行以下步骤：

1. 检出代码仓库
2. 设置Docker Buildx
3. 登录DockerHub
4. 扫描`images`目录中的所有`.tar`文件
5. 逐个加载和推送镜像
6. 检查镜像是否已存在，避免重复推送
7. 清理本地镜像缓存

## 注意事项

- 确保上传的镜像tar文件是通过`docker save`命令正确生成的
- 镜像名称和标签会从tar文件中自动提取
- 大型镜像文件可能需要较长的上传和推送时间
- 建议不要将tar文件提交到Git仓库，可以通过其他方式上传到`images`目录

## 示例

```bash
# 1. 保存本地镜像
docker save -o images/my-app-1.0.0.tar myregistry/my-app:1.0.0

# 2. 提交到Git仓库（如果tar文件很大，建议使用Git LFS或其他方式）
git add .
git commit -m "添加新的Docker镜像"
git push origin main

# 3. GitHub Actions会自动运行并推送镜像到DockerHub
```