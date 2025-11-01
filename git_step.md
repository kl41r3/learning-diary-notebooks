### Upload to Git
- `cd user/my-project`: change to current working directory 
- `git init`: initialize project
- `git add A.txt` or `git add .`: add to staging area
- `git commit -m "the message you want to convey"`: commit to git

### Push to GitHub
- `git remote add origin https://github.com/your-username/your-repository.git`: associate with remote repository
- `git push -u origin main`: link local branch with remote branch 

### If u already have a repository on GitHub
`add`-`commit`-`push`

### Clone from GitHub
- `git clone https://github.com/username/repository.git`: clone the project

### If u wanna contribute to a repository
- `fork` to your own profile. `clone` to local 
- `git remote add upstream https://github.com/username/repository.git`: add original author as upstream
- `git remote -v`: check. Result should be `origin` my repo, `upstream` the original repo.
- `git checkout -b feature/my-branch (upstream/colleague-feature)`: create your own branck
- `git pull upstream main`: pull upstream code
- `git push origin feature/your-change`: push to my change.
- request for *Pull Request* on GitHub

- `git remote set-url origin https://github.com/kl41r3/MemoryOS.git`


### Branch related
- `git branch`: look up branches
- `git checkout main`: change to branch main


### Branch name
功能开发分支
```
feature/user-login
feature/payment-integration
feature/new-dashboard
```

修复分支
```
fix/login-error
bug/navbar-responsive
hotfix/critical-security-patch
```

发布分支
```
release/v1.2.0
release/2023-12-01
```

调试/实验分支
```
debug/auth-flow
debug/performance-issue
experiment/new-ui-design
```


| 前缀 | 用途 | 示例 |
|------|------|------|
| `feature/` | 新功能开发 | `feature/user-profile` |
| `bug/` 或 `fix/` | 修复bug | `bug/login-issue` |
| `hotfix/` | 紧急修复 | `hotfix/security-patch` |
| `release/` | 发布版本 | `release/v2.1.0` |
| `debug/` | 调试分支 | `debug/api-response` |
| `experiment/` | 实验性功能 | `experiment/ai-chat` |
| `refactor/` | 代码重构 | `refactor/database-connection` |
| `docs/` | 文档更新 | `docs/api-documentation` |





