/*檢查網路*/
dhcpcd

/*分割磁碟區*/
//查看目前磁碟分割狀況
#lsblk
//sda 代表第一顆硬碟 sdb 代表第二顆 類推
#fdisk /dev/sda

/*設定分割區域數目及大小*/
Command(m for help):
#o

Command(m for help):
//nwe 一個新磁區
#n

Partition type
  p primary
  e exended
Select (default p):
//選擇類型
#p

Partition number (1-4):
//設定第幾區
#1
First sector:
//開始位置 ENTER 直接設定初值或接續之前的區段
#
Last sector:
//設定結束位置  ENTER 會直接把剩餘的位置都分配至此
#+250G
//重複以上直至分配完成
Command(m for help):
//寫入 離開
#W


//格式化硬碟 sda1 第一顆硬碟的第一區
#mkfs.ext4 /dev/sda1
//重複以上直至分配完成 sda2類推 都要執行格式化


//掛載硬碟
#mount /dev/sda1 /mnt
#mkdir /mnt/home
#mount /dev/sda2 /mnt/home

//下載Arch Linux的檔案至硬碟
#pacstrap /mnt base

//建立新系統的fstab,fstab描述硬碟分割區與掛載方式
#genfstab -p -U /mnt > /mnt/etc/fstab

//arch-chroot是Arch Linux live cd版的chroot。
//從這個步驟開始所有的指令都在新系統下面進行。
//live cd的/mnt從現在開始變成你所看到的/
#arch-chroot /mnt

//建立開機映像
//開機時initramfs會被載入記憶體執行，然後載入核心
#mkinitcpio -p linux

//安裝並設定開機程式
#pacman -S grub
//將 /dev/sda 改成您安裝 Arch 的硬碟代號。
//不要加上分割區號碼 (不要使用 sdaX)。
#grub-install --target=i386-pc --recheck /dev/sda --force
#grub-mkconfig -o /boot/grub/grub.cfg

//退出chroot回到live cd環境
#exit
//重開機
#reboot

//找出網路界面名稱
//網路界面名稱會是像enp4s0、wlp1s0的字串。
//en開頭的界面為有線網路，wl開頭的界面則為無線網路。
#ip addr

//啟動DHCP
#systemctl start dhcpcd@<interface>
//設定開機後自動啟動DHCP
#systemctl enable dhcpcd@<interface>

//安裝繪圖 X.org
#pacman -S xorg-server xorg-xinit xorg-twm xorg-apps xorg-xclock xterm xf86-video-fbdev  xfwm4
//測試應該跳出三個視窗   確認成功後 重新開機
#startx
#reboot

//安裝fxce4
#pacman -S  xfce4 xfce4-goodies
//執行 桌面
#startxfce4

//if you can't start by users
//try this command
//xfconf-query -c xfce4-session -p /sessions/Failsafe/Client0_Command -t string -sa xfwm4

//設定無線網路
//安裝軟體
#pacman -S netctl wpa_supplicant

//複製範例設定檔  建立自己的設定檔
#cp /etc/netctl/examples/wireless-wpa /etc/netctl/your_file_name
//撰寫 設定檔
#nano /etc/netctl/your_file_name
//更改以下三項

Interface=wif網路界面
ESSID='your wifi name'
Key='your wifi password'

ctrl+'o'   ctrl+'x'

//啟動網路
#netctl start your_file_name
//設定開機自動連線
#netctl enable your_file_name
#reboot

//設定語系編碼
#LANG=en_US.UTF-8
//安裝中文輸入法
#pacman -S ibus ibus-typing-booster ibus-chewing ibus-pinyin ibus-table-chinese ibus-anthy ibus-kkc ibus-skk

//新增ibus設定檔
#nano ~/.bashrc
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus

//桌面 Applications -> Setting ->S ession and Startup -> Application Autostart
//加入  ibus   尋找路徑   /usr/bin/ibus-daemon

//安裝字體
#pacman -S adobe-source-han-sans-otc-fonts

//重開機後 應該在右上角可以選擇輸入法  去新增新酷
#reboot  

//新增使用者
#useradd  -m -g users ( -G whell ) -s /bin/bash user_name
//設定使用者密碼
#passwd  password user_man

//安裝sudo
#pacman -S sudo

//編輯sudo
#visudo
//此為 vi 模式  請查詢使用方法
# sudoers file.
#
# This file MUST be edited with the 'visudo' command as root.
#
# See the sudoers man page for the details on how to write a sudoers file.
#
# Host alias specification
# User alias specification
# Cmnd alias specification
# Defaults specification
# User privilege specification
root    ALL=(ALL) ALL
user_name    ALL=(ALL) ALL                  <==這一行可以加入，使 test 這個人可以使用 sudo
# Uncomment to allow people in group wheel to run all commands
# %wheel        ALL=(ALL)       ALL                              <==將這一行註解符號取消
# Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL
# Samples
# %users  ALL=/sbin/mount /cdrom,/sbin/umount /cdrom
# %users  localhost=/sbin/shutdown -h now   


//安裝 chrom atom
//注意chrom 無法在 root 下執行
#pacman -S chromium

#pacman -S atom
