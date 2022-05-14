#How to configure git
http://hitsz-lab.gitee.io/os-labs-2021/tools/
## Build an SSH key
* 1. ssh 在终端中，输入以下指令以生成ssh密钥对:
    >ssh-keygen -t ed25519 -C "name_of_ssh_keypair"  

    密钥对的名字在教程中常用邮箱地址代替，这是一个某种意义上的传统。此外，为了兼容性考虑，不建议在密钥对名字中使用除了ASCII之外的任何字符。

* 2. >cat ~/.ssh/id_ed25519(Private key)
    cat ~/.ssh/id_ed25519.pub(Public key)
* 3. open github
    >setting -> SSH and GPG keys -> New SSH key
* 4. copy the public key on it
* 5. >ssh -T git@github.com (if neccessary input "yes")
## A repository
* 1. under a directory, open terminal
* 2. >git init
* 3. >git status
* 4. >git add .
* 5. >git commit -m "something"
* 6. >git remote add origin SSH...
* 7. >git push -u orgin master
