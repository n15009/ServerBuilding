#section1
##手順
###CentOSのインストール
1. 先生からUSBを借りる

2. copyする

3. 5秒待つ

4. **CentOS-7-x86_64-Minimal-1511.iso**があるのを確認

5. virtualboxを起動し新規をクリック

6. 仮想マシンの作成画面でエキスパートモードを選択

7. 名前の枠に**centos**と入力

8. 自動でタイプが**Linux**、バージョンが***Red Hat(64bit)***となっているのを確認

9. メモリーサイズを**1024MB**に設定する

10. ハードディスクで**仮想ハードディスクを作成する**にチェックし作成をクリック

11. 次の画面でファイルサイズを**8GB**に設定し作成をクリック

12. メイン画面で作成した**centos**を選択し起動をクリック

13. 起動ハードディスクを選択画面で**CentOS-7-x86_64-Minimal-1511.iso**を選択を選択し起動をクリック

14. 待つ（結構待つ）

15. 言語選択の画面で**日本語**を選択し続行をクリック(言語は人による)

16. 次の画面でちょっと待ってインストールの開始を選択

17. インストール中にROOTパスワードとユーザの作成を設定する（ユーザの作成時**このユーザを管理者にする**にチェック)

18. 完了したら再起動

19. ネットワークの設定でアダプター2にホストオンリーアダプターを設定する

20. **/etc/sysconfig/network-script**に行きifcfg-enp0s3とifcfg-enp0s8というファイルのBOOTPROTOをBOOTPROTO=dhcpに、さらにONBOOTをONBOOT=yesに変更する（この時rootユーザーじゃないと変更の許可がなく保存できないのでrootに変身する）

21. 変更が終わったらアップデートコマンドを実行

    `$ service network update`

22. そしたらIPアドレスが取得できているはずなので確認

    `$ ip a`

23. IPアドレスの確認がとれたら自分のターミナルから

	$ ssh virtualboxのホスト名@取得したIPアドレス

   を実行しvirtualboxとの接続を確認

24. 続いてyumとwgetのproxyの設定を行う

25. /etc/yum.confに行き以下の追記をする

    `proxy=http://IPアドレス(proxyのやつ):ポート番号`

    `proxy=https://IPアドレス(proxyのやつ):ポート番号`

26. 追加したら

    `$ yum update`

    を実行しアップデート

27.  そしてすぐさまwgetをゲットするためにコマンド

    `$ yum install wget`

    を実行する

28. wgetをインストールできたら/etc/wgetrcファイルに以下を追記

    `http_proxy=http://IPアドレス(proxyのやつ):ポート番号`

    `https_proxy=http://IPアドレス(proxyのやつ):ポート番号`

29. 追記したらもう一度アップデートを行う

    `$ yum update`

30. Apache HTTP serverのインスール（以下のコマンドを実行）
    
    `$ yum install httpd`

31. Apache を起動させる（以下のコマンド）
    
    '$ service httpd start

32. 起動出来てるか確認

    `$ service httpd status`

    ` Active: active (running)と表示されればOK `

33. mySQLのインストール（以下のコマンドを実行）
    
    `$ yum install mysql mysql-server mysql-devel`

34. mySQLを起動させる（以下のコマンド）
    
    `$ service mysqld start`
    
    ` Starting mysqld (via systemctl)               [ OK ]`

    となれば起動OK

35. mySQLにアクセスする（以下のコマンド）

    `$ mysql -p`

     パスワードを求められるのでパスワードを入力

36. しかしERROR 1045 (28000): Access denied for user 'root'@'localhost' (using p    assword: YES)と表示されアクセス出来ない

37. 原因はmySQLのパスワードが違うらしいがわからないので再設定する

38. まずmySQLを止める

    `$ service mysqld stop`
    
    `Stopping mysqld:                               [ OK ]`

    となればmySQLの停止が完了

39. 続いてmysqlをセーフモードで起動させる（以下のコマンド）
    
    `$ mysqld_safe --skip-grant-tables`

40. コマンドを実行したら別のターミナルを開きmySQLを実行する

    `$ mysql -p`

41.


--------------------------------------------------------------------------

 
