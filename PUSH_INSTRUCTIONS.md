# 推送到 GitHub 的两种方式

## 方式 1：使用 Personal Access Token (PAT) - 推荐

1. 在 GitHub 上创建 Personal Access Token：
   - 访问：https://github.com/settings/tokens
   - 点击 "Generate new token (classic)"
   - 勾选 `repo` 权限
   - 复制生成的 Token

2. 执行以下命令（将 `YOUR_TOKEN` 替换为你的 Token）：

```bash
cd /Users/xiaoxu.wu/workspace-AI/爬虫
git remote set-url origin https://YOUR_TOKEN@github.com/yiranleng/cube.git
git push -u origin main
```

或者使用环境变量（更安全）：

```bash
export GIT_TOKEN=your_token_here
git remote set-url origin https://${GIT_TOKEN}@github.com/yiranleng/cube.git
git push -u origin main
```

## 方式 2：使用 SSH

1. 确保已配置 SSH key 并添加到 GitHub

2. 执行：

```bash
cd /Users/xiaoxu.wu/workspace-AI/爬虫
git remote set-url origin git@github.com:yiranleng/cube.git
git push -u origin main
```

## 当前状态

- ✅ 本地仓库已初始化
- ✅ 所有文件已提交（217 个文件）
- ✅ 分支已设置为 `main`
- ✅ Remote 已配置为 `https://github.com/yiranleng/cube.git`

只需完成上述任一方式的推送即可。
