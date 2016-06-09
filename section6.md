#6-0ＡＷＳコマンドラインインターフェースのインストール
やったれ
    $ aws ec2 describe-instances

    $ aws ec2 start-instances --instance-ids i-1348636c

    $ aws sns publish --topic-arn arn:aws:sns:us-east-1:546419318123:OperationsError --message "Script Failure"

    $ aws sqs receive-message --queue-url https://queue.amazonaws.com/546419318123/Test


#6-1AWS EC2 + Ansible
まずAnsibleが出来てなかったのでそれをやった。

###ＡＷＳのＥＣ２でインスタンスを作成する。

「ＥＣ２」ー「インスタンス」ー「インスタンスの作成」ー「Amazon Linux AMI 2016.03.1 (HVM), SSD Volume Type - ami-29160d47」ー「汎用、t2.micro」ー「確認と作成」ー「セキュリティグループの編集」ー「新しいセキュリティグループの作成」ー「セキュリティグループ名を学籍番号に」ー「タイプ（SSH,HTTP,HTTPS)を選択」ー「確認と作成」

###キーペアの作成

「キーペアの作成」ー「学籍番号を入力」ー「作成」

キーペア名.pemが出来た。

grusniに作ったキーとAnsibleセットを持って行く。

ファイルhostsとplaybook.ymlのipアドレスをインスタンスのパブリックipに変更する。

インスタンス画面で自分のインスタンスをチェックし「接続」をクリック

    chmod 400 n15009.pem

をやれって書いてあるのでキーのところで実行。

そして

    ssh -i "キーペア名.pem" ec2-user@ec2-54-238-226-216.ap-northeast-1.compute.amazonaws.com

でＥＣ２にログイン

で、Ansible せっとがあるところに戻りAbsibleを実行。

ブラウザでパブリックipを打つとwordpressが出来てた！！ラッキー


6-2 AMI

環境の構築が終わったら、ＡＭＩの作成。インスタンスの自分のインスタンスチェックし「アクション」ー「イメージ」ー「イメージの作成」
するとＡＭＩに自分のやつが出来ているのでそれをチェックし「作成」
インスタンスにもう一個できていたのでそいつのパブリックＤＮＳ名をブラウザで打ったら出来た。

#6-3 s3
amazon AWSのホームでs3を選択しバケットの作成をクリック
バケット名を入力（ここでは自分の学籍番号を入力)し作成をクリック

単一フォルダコマンドで複数のファイルを再帰的にアップロード、ダウンロードできます。

    $ aws s3 cp myfolder s3://mybucket/myfolder --recursive
    upload: myfolder/file1.txt to s3://mybucket/myfolder/file1.txt
    upload: myfolder/subfolder/file1.txt to s3://mybucket/myfolder/subfolder/file1.txt

