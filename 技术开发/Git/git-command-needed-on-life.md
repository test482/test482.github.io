# 在实际中用到的 git 命令

1. 编辑未提交至 origin 的刚刚写的 commit message

    `git commit --amend`

2. 基于两次 commit 之间的差异生成 patch 文件

    `git format-patch $OlderCommitHash..$NewerCommitHash`

    > `OlderCommitHash` 是时间早的 commit 的哈希值
    >
    > `NewerCommitHash` 是时间更晚的 commit 的哈希值
    >
    > `--numbered-files` 选项, 则文件名只有编号, 不包含提交信息
    >
    > `-o <dir>, --output-directory <dir>` 指定 patch 的存放目录

3. 应用 patch

    `git am --abort` 放弃之前的应用 patch 时的缓存

    `git am xxx.patch` / `git am patch/*.patch` 应用补丁

    or

    检查 patch 文件：`git apply --stat newpatch.patch`

    检查能否应用成功：`git apply --check newpatch.patch`

    打补丁：`git am --signoff < newpatch.patch`

4. 绕过 git pre-hook 提交 commit

    `git commit --no-verify -m ""`

5. 使 fork 的仓库同步原仓库

    `git remote -v` 查看配置

    `git remote add upstream $UPSTREAM_URL`

    `git fetch upstream`

    `git checkout $BRANCH_NAME`

    `git merge upstream/$BRANCH_NAME`

    然后 push 到 fork 的仓库

    `git push origin $BRANCH_NAME`

6. 在本地和远端删除分支

    删除本地分支:

    `git branch -d $BRANCH_NAME`

    删除远端分支:

    `git push origin --delete $BRANCH_NAME`

7. 从 git 存储库中删除一个文件，但不想从本地文件系统中删除它

    `git rm --cached xxx.file`

    `git rm --cached -r xxxdir`

8. 把现有的修改合并到上个 commit 内中

    > 以便保持 commit 记录整洁
    >
    > 和协作中并不知道使用 squash and merge 合并 pr 的仓库管理人

    `git commit --amend`

    `git push --force` // 一般是强制 push 到自己的 repo, 切忌 push 到协作 repo.
