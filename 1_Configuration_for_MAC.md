# MySQL Mac本地配置

## 1 环境变量设置

```shell
cd ~
sudo vim ./.bash_profile
# mysql 进程环境变量
export PATH=$PATH:/usr/local/mysql/bin
# mysql 服务器环境变量
export PATH=$PATH:/usr/local/mysql/binexport PATH=$PATH:/usr/local/mysql/support-files

source ~/.bash_profile
```