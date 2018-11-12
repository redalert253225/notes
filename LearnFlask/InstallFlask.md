建立虛擬環境 安裝Flask
--

* make a folder which storage venv

$ mkdir Learn_Flask

$ cd Learn_Flask

$ python3 -m venv myVenv    #create  venv  虛擬環境


$ source myVenv/bin/activate  #activate  venv      

#$ venv\Scripts\activate      if on  windown   activate  venv


* if successful you will see  
(venv) [yu@archlinux Learn_Flask]$  

(myVenv)$ pip install --upgrade pip     #更新pip

(venv)$ pip install flask     #nstall flask  如果是舊版python 要先裝pip

* open python3   try  import flask
