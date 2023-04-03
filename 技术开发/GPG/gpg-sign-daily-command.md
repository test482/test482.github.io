# GPG 签名常用指令

- 列出拥有的公钥和私钥的长形式 GPG 密钥</br>
    `gpg --list-secret-keys --keyid-format=long`

- 告诉 git 使用哪一个私钥签名</br>
    `git config --global user.signingkey xxxx`

- 对于 Windows, 你可能需要告诉 git `gpg.exe` 的位置</br>
    `git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"`
