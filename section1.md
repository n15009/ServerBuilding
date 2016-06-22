#section1
##手順
###CentOSのインストール
######文章で書くと分かりづらいので流れで説明します。

#####1.-------->先生からUSBを借りcopyする

#####2.-------->**CentOS-7-x86_64-Minimal-1511.iso**があるのを確認

#####3.-------->virtualboxを起動し新規をクリック

#####4.-------->仮想マシンの作成画面でエキスパートモードを選択

#####5.-------->名前の枠に**centos**と入力

#####6.-------->自動でタイプが**Linux**、バージョンが***Red Hat(64bit)***となっているのを確認

#####7.-------->メモリーサイズを**1024MB**に設定する

#####8.-------->ハードディスクで**仮想ハードディスクを作成する**にチェックし作成をクリック

#####9.-------->次の画面でファイルサイズを**8GB**に設定し作成をクリック

#####10.-------->メイン画面で作成した**centos**を選択し起動をクリック

#####11.-------->起動ハードディスクを選択画面で**CentOS-7-x86_64-Minimal-1511.iso**を選択を選択し起動をクリック

###11.5.....待つ!!（結構待つ）

#####12.-------->言語選択の画面で**日本語**を選択し続行をクリック(言語は人による)

#####13.-------->次の画面でちょっと待ってインストールの開始を選択

#####14.-------->インストール中にROOTパスワードとユーザの作成を設定する（ユーザの作成時**このユーザを管理者にする**にチェック)

#####15.-------->完了したら再起動

#####16.-------->ネットワークの設定でアダプター2にホストオンリーアダプターを設定する

####17.-------->ネットワークの設定

    /etc/sysconfig/network-scriptに行き

    ifcfg-enp0s3とifcfg-enp0s8というファイルのBOOTPROTO

    をBOOTPROTO=dhcpに、

    さらにONBOOTをONBOOT=yesに変更する

    ⚠（この時rootユーザーじゃないと変更の許可がなく保存できないのでrootに変身する）

#####18.-------->変更が終わったらアップデートコマンドを実行

    $ service network restart

#####19.-------->そしたらIPアドレスが取得できているはずなので確認

    $ ip a

#####20.-------->IPアドレスの確認がとれたら自分のターミナルから    
 
    $ ssh virtualboxのホスト名@取得したIPアドレス

を実行しvirtualboxとの接続を確認

#####20.-------->続いてyumとwgetのproxyの設定を行う

**/etc/yum.conf**に行き以下の追記をする

    proxy=http://IPアドレス(proxyのやつ):ポート番号
    
    proxy=https://IPアドレス(proxyのやつ):ポート番号

#####21.-------->追加したら

    $ yum -y update
    
を実行しアップデート

#####22.-------->そしてすぐさまwgetをゲットするためにコマンド

    $ yum install wget
    
を実行する

#####23.-------->wgetをインストールできたら/etc/wgetrcファイルに以下を追記

    http_proxy=http://IPアドレス(proxyのやつ):ポート番号
    
    https_proxy=http://IPアドレス(proxyのやつ):ポート番号

これで一通りの設定はOK！！

##Apache HTTP serverのインスール（以下のコマンドを実行）
    
    $ yum install httpd

####Apache を起動させる（以下のコマンド）
    
    $ service httpd start

起動出来てるか確認

    $ service httpd status

    Active: active (running)と表示されればOK

##mySQLのインストール
######（ここは苦戦してしまったのでエラーとかも含めて書いています）
mSQLのインストール

    $ yum install mysql mysql-server mysql-devel

mySQLを起動させる（以下のコマンド）
    
    $ service mysqld start
    
    Starting mysqld (via systemctl)               [ OK ]

となれば起動OK

mySQLにアクセスする（以下のコマンド）

    $ mysql -p

パスワードを求められるのでパスワードを入力

しかし
  
    ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)

と表示されアクセス出来ない

原因はmySQLのパスワードが違うらしいがわからないので再設定する

まずmySQLを止める

    $ service mysqld stop
    
    Stopping mysqld:                               [ OK ]

となればmySQLの停止が完了

続いてmysqlをセーフモードで起動させる（以下のコマンド）
    
    $ mysqld_safe --skip-grant-tables

コマンドを実行したら別のターミナルを開きmySQLを実行する

    $ mysql -p

mysqlていう databaseが存在するのでそちらに移動

    mysql> use mysql;

    Database changed

続いてユーザー情報の変更でパスワードを変更する

    mysql> UPDATE user SET Password=PASSWORD（’新しいパスワード’）WHERE User=’root’;
    
これでOK

    $ mysql -u adminusername -p


でmysqlに接続しここはチャンスなのでコマンドを撃    ちまくる

     `mysql> CREATE DATABASE databasename;`

     `mysql> GRANT ALL PRIVILEGES ON databasename.* TO "wordpressusername"@"host      name"IDENTIFIED BY "password";`

     `mysql> FLUSH PRIVILEGES;`

     `mysql> EXIT`

これでmySQLの設定は完了

   
###mySQL他の方法


まずmariadbなる変な奴がいるので消す

    $ yum list installed | grep maria`

このコマンドで存在を確認し

    $ yum -y remove mariadb-libs

これで消去

続いてmySQLのインストール

    $ yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

    $ yum -y install mysql
    $ yum -y install mysql-devel
    $ yum -y install mysql-server
    $ yum -y install mysql-utilities

インストールしたらmySQLの起動

    $ service mysql start

続いてパスワードの設定

    $ /usr/bin/mysql_secure_installation

新しいパスワードを聞かれるので設定し完了
 

##PHPのインストール（以下のコマンド）

    `$ yum install php php-mbstring php-mysql`

####phpの起動の確認

      /var/www/htmlの下にindex.phpというファイルを作成し以下を記入し保存

    ` <?php phpinfo(); ?>`
    
     そして```IPアドレス/index.php```と入力し青っぽいphpの画面が出れば起動してい     る


##wordpressのインストール

    `$ wget https://ja.wordpress.org/latest-ja.tar.gz`

    `$ tar -xzvf latest-ja.tar.gz`

    /var/www/htmlの下にwordpressの中身を展開

    `$ mv wordpress/* ./`
    
動かなかったら

    $ service httpd restart

    $ service mysqld restart

を試して見るべし  

-------------------------------------------------------------------------

 
