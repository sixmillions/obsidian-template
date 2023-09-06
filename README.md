---
dg-home: true
dg-publish: true
---
# Home Page

[[obsidian/00-readme|模板说明]]

## Git

```bash
git init
git config user.name sixmillions
git config user.email liubw95@163.com

git add .
git commit -m 'Init'

git branch -M main
git remote add origin https://github.com/sixmillions/obsidian-template.git
git push -u origin main
```

当前项目代理配置

```bash
git config http.proxy http://10.203.49.204:8080
git config https.proxy http://10.203.49.204:8080
```