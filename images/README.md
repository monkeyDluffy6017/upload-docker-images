# Images 目录

这个目录用于存放要推送到DockerHub的Docker镜像tar文件。

## 使用方法

1. 使用`docker save`命令将Docker镜像保存为tar文件：

```bash
# 示例：保存镜像
docker save -o ai-quota-shenma-0.0.1.tar zgsm/ai-quota-shenma:0.0.1
```

2. 将生成的tar文件放入此目录

3. 提交代码到Git仓库，GitHub Actions会自动推送镜像到DockerHub

## 文件命名建议

建议使用有意义的文件名，例如：
- `ai-quota-shenma-0.0.1.tar`
- `my-app-v1.2.3.tar`
- `nginx-custom-latest.tar`

## 注意事项

- 确保tar文件是通过`docker save`命令生成的
- 如果文件很大（>100MB），考虑使用Git LFS或其他方式管理
- 镜像名称和标签会从tar文件内容中自动提取，不依赖文件名