/usr/local/sbin/export_root_history.sh
ls -l /root/his.txt /root/.bash_history
cat /root/his.txt
wc -l /root/.bash_history
wc -l /root/.bash_history
history
crontab -e
crontab -l
systemctl enable --now crond
systemctl status crond
timedatectl
timedatectl set-timezone Asia/Shanghai
timedatectl
echo "720进程管理的实验"
echo $$
pstree -p
ls
touch null
ping baidu.com > null &
ping qq.com > null &
ping weixin.com > null &
ps -p 1668
jobs
ps -p 1669 1669 1670
ps -p 1669 1669 1670 -axo pid,ppid,stat,command
ps -help
ps --help
ps -auxo
ps -auxo pid
ps -axo pid
ps -axo pid,ppid,stat,command
ps -axo pid,ppid,stat,command |grep 1668 1669 1670
ps -axo pid,ppid,stat,command |grep 1668,1669,1670
touch mark
ps -axo pid,ppid,stat,command |grep 1668 > mark
cat mark
ps -axo pid,ppid,stat,command |grep 1669 > mark
ps -axo pid,ppid,stat,command |grep 1670 > mark
cat mark
ps -axo pid,ppid,stat,command |grep 1668 >> mark
ps -axo pid,ppid,stat,command |grep 1669 >> mark
cat mark
jobs
fg 1
bg 1
fg 1
pstree -p 
pkill ping
pstree
pstree -p
kill -9 1668
yum docker
yum install docker
bgpstree -p
which httpd
systemctl status hpptd
httpd
yum install httpd
which httpd
systemctl status httpd
httpd
pstree
ps aux|grep httpd
ps auxf |grep httpd
pstree -p
ps auxfH |grep httpd
ps -efH |grep httpd
pkill httpd
ps -aux |grep httpd
systemctl start httpd
ps -aux |grep httpd
nophup ping baidu.com > null &
nohup ping baidu.com > null &
ls
cat null
tail -f null
ps -p 
pstree -p
pkill httpd
pstree -p
kill -15 5517
cat null
ls
sz his.txt
source ./.bashrc
history
vim his.txt 
vim ./.bash_history
