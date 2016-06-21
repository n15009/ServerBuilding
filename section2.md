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

#ホストオンリーアダプタの設定
サーバーを設定したあと、動作確認し接続するためのIPアドレスを設定します。また、そのためのNICを追加します。
Vagrantfileの
    
    vagtant.congfigure(2) do | config|

から一番最後の

    end

の間に

    config.vm.network "private_network", ip:"192.168.56.129"

と書き仮想マシンのIPアドレスを設定
Vagrantfileの設定変更の反映

    $ vagrant reload

|仮想マシンの起動

    $ vagrant ssh

#2-2Wordpressを動かす

###nginxのインストール

    $ sudo yum -y install http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm  
    $ yum install --enablerepo=epel nginx 

###phpのインストール

    $ yum -y install php php-fpm php-mysql

/etc/php-fpm.d/www.confの中でapacheをnginxに書き換える

    user = apache → user = nginx
    group = apache → group = nginx

更に、/etc/nginx/conf.d/default.confの中を

    root   /var/www/;
    index  index.php;

     location ~ \.php$ {   
            root           /var/www/;  
            fastcgi_pass   127.0.0.1:9000;  
            fastcgi_index  index.php;  
            fastcgi_param  SCRIPT_FILENAME  /var/www$fastcgi_script_name;  
            include        fastcgi_params;  
     }

↑上記に書き換える

nginxとphp-fpmの自動起動設定

    $ systemctl enable php-fpm.service
    $ systemctl enable nginx.service

###MariaDBのインストール

    $ yum -y install mariadb mariadb-server

###MariaDBの起動

    $ systemctl start mariadb 

    $ systemctl enable mariadb

###betabaseを作成

    MariaDB [(none)]> create database database名;
    MariaDB [(none)]> grant all privileges on database名.* to "username"@"localhost"identified by "password";
    MariaDB [(none)]> flush privileges;
    MariaDB [(none)]> exit
#Wordpressダウンロード

    $ wget http://wordpress.org/latest.tar.gz    

解凍

    $ tar -xvf latest-ja.tar.gz

所有者とグループを変える

    $ chown -R nginx:nginx wordpress  

default.confの設定を変える

    $ vi /etc/nginx/conf.d/default.conf  
    
    rootをwordpressがあるとこに変更する

    fastcgi_param SCRIPT_FILENAME  ←に$document_root$fastcgi_script_name;  を追加

終わったらnginxの再起動を忘れずに

    $ systemctl restart nginx  

    192.168.56.129/wp-admin/install.php 

    でWordpressを開く


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
######⚠(エラーの種類によります！エラーが出てない場合はやらなくてOK)

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

###php.iniの作成だぜ
このままだとphpが動かないので
 
    $ find / -name "php.ini-development" -ls
    $ cp php.ini-development /usr/local/lib/php.ini
    $ /usr/local/apache2/bin/apachectl restart

ブラウザで

     vagrantのIPアドレス/wp-admin/install.php

につないでwordpressへ！！

⚠多分localhostのところに127.0.0.1と入力した方がいいときもある

#ベンチマークを取るぜ

    $ sudo apt install apache2-utils

    $ ab -n 100 -c 100 http://192.168.56.128/wp-admin/