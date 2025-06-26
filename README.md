# Upload Docker Images

这个项目用于自动将Docker镜像推送到多个DockerHub账户。

## 功能特性

- 支持多个DockerHub账户的镜像推送
- 自动扫描指定目录中的所有`.tar`格式的Docker镜像文件
- 通过GitHub Actions自动推送镜像到对应的DockerHub账户
- 智能检测镜像是否已存在，避免重复推送
- 支持手动触发和代码变更自动触发

## 目录结构

```
upload-docker-images/
├── images-levy001/            # 存放推送到Levy001账户的Docker镜像tar文件
│   ├── ai-statistics-shenma-0.0.3.tar
│   └── README.md
├── images-zgsm/              # 存放推送到ZGSM账户的Docker镜像tar文件
│   └── README.md
├── .github/
│   └── workflows/
│       └── push-images.yml    # GitHub Actions工作流
└── README.md
```

## 账户配置

### Levy001 账户 (images-levy001目录)
使用原有的DockerHub凭据：
- `DOCKERHUB_USERNAME`: Levy001 DockerHub用户名
- `DOCKERHUB_TOKEN`: Levy001 DockerHub访问令牌

### ZGSM 账户 (images-zgsm目录)
使用新的DockerHub凭据：
- `DOCKERHUB_USERNAME_ZGSM`: ZGSM DockerHub用户名
- `DOCKERHUB_TOKEN_ZGSM`: ZGSM DockerHub访问令牌

## 使用方法

### 1. 准备Docker镜像

根据目标账户，将镜像保存到对应目录：

```bash
# 推送到Levy001账户
docker save -o images-levy001/your-image-name.tar your-registry/your-image:tag

# 推送到ZGSM账户
docker save -o images-zgsm/your-image-name.tar your-registry/your-image:tag
```

### 2. 配置DockerHub凭据

在GitHub仓库设置中添加以下Secrets：

#### Levy001账户配置：
- `DOCKERHUB_USERNAME`: Levy001的DockerHub用户名
- `DOCKERHUB_TOKEN`: Levy001的DockerHub访问令牌

#### ZGSM账户配置：
- `DOCKERHUB_USERNAME_ZGSM`: ZGSM的DockerHub用户名
- `DOCKERHUB_TOKEN_ZGSM`: ZGSM的DockerHub访问令牌

要创建DockerHub访问令牌：
1. 登录对应的DockerHub账户
2. 进入Account Settings > Security
3. 点击"New Access Token"
4. 复制生成的令牌

### 3. 触发推送

有以下几种方式触发镜像推送：

#### 自动触发
- 当你向`main`分支推送包含`images-levy001`目录变更的代码时，会触发Levy001账户的镜像推送
- 当你向`main`分支推送包含`images-zgsm`目录变更的代码时，会触发ZGSM账户的镜像推送
- 当你创建针对`main`分支且包含相应目录变更的Pull Request时

#### 手动触发
1. 进入GitHub仓库的Actions页面
2. 选择"Push Docker Images to DockerHub"工作流
3. 点击"Run workflow"按钮（会同时处理两个目录）

### 4. 查看推送结果

在GitHub Actions页面可以查看推送过程和结果：
- 成功推送的镜像会显示"成功推送镜像"消息，并标明推送到的账户
- 已存在的镜像会显示"已存在于DockerHub，跳过推送"消息
- 推送失败会显示错误信息

## 工作流说明

GitHub Actions工作流会根据变更的目录自动选择对应的任务：

### push-levy001-images 任务：
1. 检出代码仓库
2. 设置Docker Buildx
3. 使用Levy001凭据登录DockerHub
4. 扫描`images-levy001`目录中的所有`.tar`文件
5. 逐个加载和推送镜像到Levy001账户
6. 检查镜像是否已存在，避免重复推送
7. 清理本地镜像缓存

### push-zgsm-images 任务：
1. 检出代码仓库
2. 设置Docker Buildx
3. 使用ZGSM凭据登录DockerHub
4. 扫描`images-zgsm`目录中的所有`.tar`文件
5. 逐个加载和推送镜像到ZGSM账户
6. 检查镜像是否已存在，避免重复推送
7. 清理本地镜像缓存

## 注意事项

- 确保上传的镜像tar文件是通过`docker save`命令正确生成的
- 镜像名称和标签会从tar文件中自动提取
- 大型镜像文件可能需要较长的上传和推送时间
- 建议不要将tar文件提交到Git仓库，可以通过其他方式上传到对应目录
- 每个目录使用独立的DockerHub凭据，确保配置正确的Secrets

## 示例

```bash
# 1. 保存本地镜像到Levy001账户目录
docker save -o images-levy001/my-app-1.0.0.tar myregistry/my-app:1.0.0

# 2. 保存本地镜像到ZGSM账户目录
docker save -o images-zgsm/zgsm-app-1.0.0.tar zgsm/zgsm-app:1.0.0

# 3. 提交到Git仓库
git add .
git commit -m "添加新的Docker镜像"
git push origin main

# 4. GitHub Actions会自动运行并推送镜像到对应的DockerHub账户
```