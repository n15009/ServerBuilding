#section0
##手順
###virtualboxのインストール

1.[virtualboxの公式サイト](https://www.virtualbox.org/wiki/Downloads)に行く　　

2.**VirtualBox 5.0.20 for Linux hosts**を選択　　

3.**Ubuntu 16.04 ("Xenial")*の*AMD64**を選択　　

4.待つ

5.起動できない

6.ターミナルからコマンド入力

    $ sudo apt-get install　virtualbox

7.ちょっと待つ

8.起動する→成功！！

-----------------------------------------------------------------------------






###vagrantのインストール

1.[vagrantの公式サイト](https://www.vagrantup.com/404.html)に行く

2.Downloadのページに行き**DEBIAN**の**64Bit**を選択

3.待つ

4.ダウンロード完了！

5.ターミナルからインストール

    $ sudo apt-get install vagrant

6.バージョン確認

    $ vagrant -v

7.**Vagrant 1.8.1**を確認し任務完了！

と思ったのですが、apt-getでインストールすると使えなくはないのですが、後々詰まることになるので

    $ sudo dpkg  -i 

でやってできます。