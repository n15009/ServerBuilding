#Ansibleによる自動化のテスト
##3-0Ansubleのインストール

    $ sudo apt-get install software-properties-common
    $ sudo apt-add-repository ppa:ansible/ansible
    $ sudo apt-get update
    $ sudo apt-get install ansible

##3-1Ansibleでwordpressを動かす

hostsファイルの作成

    $ echo 自分のサーバーのIPアドレス > hosts

pingで確認する

    $ ansible 自分のIPアドレス -i hosts -m ping --private-key ~/.vagrant.d/insecure_private_key -u vagrant -k

playbook.ymlに設定を書いていく
playbook.ymlと同じ場所にnginx,php-fpm,wordpressディレクトリの作成

    $ mkdir nginx php-fpm wordpress 
