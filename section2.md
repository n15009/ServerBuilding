#section02
#2-1Vagrantを利用したCentOS7環境の起動
USBストレージからVagrant用CentOS boxを使用
    $vagrant box add CentOS7 コピーしたboxファイル --force
Vagrantの初期設定
作業用でディレクトリを作成し、その中で初期設定を行う。
    $vagrant init
「vagrantfile」というvagantのファイルをが出来た。このファイルの中に設定が書かれているのでCentOS7を起動させるために設定を変えます
    config.vm.box = "base"
の"base"の部分を
    config.vm.box = "CentOS7"
と書き換えます。
##仮想マシンの起動

    vagrant up
##仮想マシンの停止

    vagrant halt
##仮想マシンの一時停止

    vagrant suspend
##仮想マシンの破棄

最初からやり直したい…そんな時に破棄するとCentOSが初期化されます。 またvagrant upをすると立ち上がります…

    vagrant destroy
##仮想マシンへ接続

実際の仮想マシンへはsshで接続します。

    vagrant ssh



# 2-3 Wordpressを動かす
### Apache HTTP Server 2.2のインストール↓↓

    $ wget http://ftp.yz.yamagata-u.ac.jp/pub/network/apache//httpd/httpd-2.2.31.tar.gz

###あたたまる前に解凍しちゃうぜ

    $ tar -xvf httpd_2.2.31.tar.gz

###コンパイルしようぜ!
#####(⚠httpdディレクトリの中でですよ！)

    $ ./configure

###ビルドしようぜ！

    $ make

###インストールしようぜ

    $ make install

###インストール終わってウハウハなって浮かれてるそこの君！

####このまま起動しても出来ません

    $ /usr/local/apache2/bin/apachectl start
    httpd: Could not reliably determine the server's fully qualified domain name, using localhost.localdomain for ServerName

####↑とエラーなっちゃうのでちょっといじるぜ

    $ vi /usr/local/apache2/conf/httpd.conf

####へ行き

    #ServerName www.example.com:80
    ServerName localhost:80  ←この行を追加だぜ

###ここで再起動だぜ!!

    $ /usr/local/apache2/bin/apachectl restart

####何も言われなかったらOKだぜ

#####↓これでstartしてるか確認できるぜ↓

    $ ps aux | grep httpd

###そして自分のIPアドレスをブラウザに入力し

    It works!

###と表示されればOK！

#phpのインストール

###phpインストールコマンド

    $ wget http://jp2.php.net/get/php-7.0.6.tar.bz2/from/this/mirror 

#####すぐさま自然解凍します

    $ tar -xvf mirror 

#####続いてphpディレクトリの中に行きコンパイルいたす

    $ ./configure --with-apxs2=/usr/local/apache2/bin/apxs --with-mysqli  

#####コンパイル終わって

    error: xml2-config not found. Please check your libxml2 installation.

#####というNEWエラーがでてきたので

    $ yum install -y libxml2 libxml2-devel

#####この子を取得しもういちどコンパイルし完了
######⚠(エラーが出てない場合はやらなくてOK)

####そしてビルドしようぜ！

    $ make

####インストールしようぜ！

    $ make install

####これでphpはOKだぜ！！

#mysqlのインストール
###ここではmariadbをインストールしましたよ

    $ yum -y install mariadb mariadb-devel mariadb-server

####起動しようぜ

    $ systemctl start mariadb

####確認しようぜ

    $ systemctl status mariadb

####確認終わったらdatabaseの作成

    $ mysql -p

#####パスワードを聞かれますがシカトしてEnter押しちゃえ！

####作ちゃうぜ

    MariaDB [(none)]> create database database名;
    MariaDB [(none)]> grant all privileges on database名.* to "username"@"localhost"identified by "password";
    MariaDB [(none)]> flush privileges;
    MariaDB [(none)]> exit

###リスタートしちゃうぜ

    $ systemctl restart mariadb

#Wordpressのインストール
####wordpressインストールコマンド

    $ wget http://wordpress.org/latest.tar.gz

#####解凍いたす↓

    $ tar -xvf latest.tar.gz

####wordpressディレクトリをhtdocsへ移動

    $ mv wordpress/ /usr/local/apache2/htdocs

##そして中身をぶちまけるようぜ！！
    
    htdocs$ mv wordpress/* ./

これ設定しれ

    $ vi /usr/local/apache2/conf/httpd.conf
    
    <IfModule dir_module>
        DirectoryIndex index.html ←ここに「index.php」を追加
    </IfModule>

    そして行の最後尾にこいつを追加だぜ！
      ↓            ↓           ↓
    <FilesMatch "\.ph(p[2-6]?|tml)$">
        SetHandler application/x-httpd-php
    </FilesMatch>

そして再びリスタートかける

    $ /usr/local/apache2/bin/apachectl restart

ブラウザで

     vagrantのIPアドレス/wp-admin/install.php

につないでwordpressへ！！

⚠多分localhostのところに127.0.0.1と入力した方がいいときもある

#ベンチマークを取るぜ

    $ sudo apt install apache2-utils

    $ ab -n 100 -c 100 http://192.168.56.128/wp-admin/