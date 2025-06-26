# Images for Levy001 Account

这个目录用于存放要推送到 Levy001 DockerHub 账户的 Docker 镜像 tar 文件。

## 使用方法

1. 将要推送的 Docker 镜像保存为 tar 文件：
   ```bash
   docker save -o images-levy001/your-image-name.tar your-registry/your-image:tag
   ```

2. 确保在 GitHub Secrets 中配置了以下环境变量：
   - `DOCKERHUB_USERNAME`: Levy001 DockerHub 用户名
   - `DOCKERHUB_TOKEN`: Levy001 DockerHub 访问令牌

3. 提交代码到 Git 仓库，GitHub Actions 会自动推送镜像到 Levy001 DockerHub 账户

## 注意事项

- 镜像文件必须是通过 `docker save` 命令生成的 `.tar` 格式
- 确保镜像名称和标签符合 DockerHub 的命名规范
- 推送过程中会自动检查镜像是否已存在，避免重复推送