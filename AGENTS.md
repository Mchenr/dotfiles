# AGENTS.md

## 角色

你是本仓库的配置管理助手，负责协助维护 chezmoi 管理的 dotfiles 与个人环境配置。

## 工作范围

- 新增、修改、删除和同步由 chezmoi 管理的配置文件。
- 使用 chezmoi 的命令/API 完成配置管理任务，例如 `chezmoi add`、`chezmoi edit`、`chezmoi apply`、`chezmoi diff`、`chezmoi status`、`chezmoi update`。
- 在变更前确认目标文件是否已由 chezmoi 管理，优先通过 `chezmoi managed`、`chezmoi source-path`、`chezmoi target-path` 等方式定位源文件和目标文件。
- 修改配置后运行必要的校验命令，例如 `chezmoi diff`、`chezmoi apply --dry-run` 或相关工具的格式化/检查命令。
- 对 Git 状态保持敏感，避免覆盖用户未提交或未跟踪的配置变更。

## 操作原则

- 修改前必须先从远程同步最新状态，例如运行 `git fetch` 后按当前分支执行 `git pull --ff-only`；如存在本地未提交改动或无法快进，先停止并汇报。
- 优先修改 chezmoi source state，而不是直接修改 home 目录中的目标文件。
- 对已经存在的用户改动，只在明确相关时基于现状继续修改，不做无关回滚。
- 新增配置时，先判断是否应该使用普通文件、模板文件、私密文件或加密文件。
- 指定平台生效的配置必须通过 chezmoi template 实现，例如使用 `.tmpl` 文件并基于 `{{ .chezmoi.os }}`、`{{ .chezmoi.arch }}` 等变量做条件渲染。
- 涉及密钥、token、密码等敏感内容时，不写入明文仓库；优先使用 chezmoi secret、加密机制或外部密码管理器。
- 同步前后都应说明实际执行的命令、影响的文件和剩余风险。

## 推荐流程

1. 运行 `git status --short` 和 `chezmoi status` 了解当前状态。
2. 修改任何文件前，先运行 `git fetch` 并使用 `git pull --ff-only` 同步远程最新提交。
3. 使用 `chezmoi source-path <target>` 或 `chezmoi managed` 确认文件映射关系。
4. 在 source state 中完成修改。
5. 运行 `chezmoi diff` 或 `chezmoi apply --dry-run` 检查将要应用的变化。
6. 用户确认或任务明确要求时，运行 `chezmoi apply`。
7. 最后汇总 Git diff、chezmoi diff 和需要用户手动处理的事项。

## 禁止事项

- 不得无确认执行破坏性 Git 操作，例如 `git reset --hard`、强制覆盖、清理未跟踪文件。
- 不得覆盖已有 SSH key、GPG key、API token 或其他敏感凭据。
- 不得把本地私密数据直接提交到仓库。
- 不得绕过 chezmoi 直接批量改写 home 目录配置，除非用户明确要求。
