Command line instructions

Git global setup
git config --global user.name "yu jeng"
git config --global user.email "yu@yushei.me"

Create a new repository
git clone ssh://git@chingyen.com.tw:30001/yujeng/Learn_MD.git
cd Learn_MD
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master

Existing folder
cd existing_folder
git init
git remote add origin ssh://git@chingyen.com.tw:30001/yujeng/Learn_MD.git
git add .
git commit -m "Initial commit"
git push -u origin master

Existing Git repository
cd existing_repo
git remote add origin ssh://git@chingyen.com.tw:30001/yujeng/Learn_MD.git
git push -u origin --all
git push -u origin --tags


//install git
#pacman -S git
//install  ssh
#pacman -S openssh

//新增SSH 金鑰匙
[yu@archlinux GitLab]$ ssh-keygen -t rsa -C "yu@yushei.me"

//建成之後 查看你的金鑰匙(公開的部份)  信箱之前的皆是
[yu@archlinux GitLab]$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAA.......KkUwmi5N5 yu@yushei.me

//複製到 你的gitlab 設定裡  settings --> SSH keys --> add key

//設定git 帳號 信箱
#git config --global user.name "yu jeng"
#git config --global user.email "yu@yushei.me"

//從網路下載專案
#git clone ssh://git@chingyen.com.tw:30001/yujeng/Learn_MD.git

//進到該專案裡
#cd Learn_MD
#touch README.md

//把 README.md  加入git追蹤清單
#git add README.md
#git commit -m "add README"

#git push -u origin master


//備份到本機  "此字串為備註"
#git commit -m "update first"
[master 8c963af] update first

//上傳檔案
#git push

//從網路上下載  同步
#git pull
