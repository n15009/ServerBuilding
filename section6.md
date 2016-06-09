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


###AMI

環境の構築が終わったら、ＡＭＩの作成。インスタンスの自分のインスタンスチェックし「アクション」ー「イメージ」ー「イメージの作成」
するとＡＭＩに自分のやつが出来ているのでそれをチェックし「作成」
インスタンスにもう一個できていたのでそいつのパブリックＤＮＳ名をブラウザで打ったら出来た。

#6-2 AMIMOTO

EC2を選択します。

インスタンスの作成をクリック。

AWS Marketpkaceを選択。

検索画面で「AMIMOTO」と入力。

「WordPress powered by AMIMOTO (HVM)」を選択。

インスタンスを選択します。（6-1でやった感じ)

作成をクリック。

キーペアの作成（既存のキーペア、自分の学籍番号のやつ）チェックボックスにチェックし「作成」

「インスタンスの作成」

起動したら左メニューのエラスティックＩＰを選択します。

「新しいアドレスの割り当て」をクリック

「関連付ける」をクリック

自分のＥＩＰをチェックし「アクション」ー「アドレスの関連付け」

さっき作ったインスタンスのインスタンスIDを入力



#6-3 s3
amazon AWSのホームでs3を選択しバケットの作成をクリック
バケット名を入力（ここでは自分の学籍番号を入力)し作成をクリック

単一フォルダコマンドで複数のファイルを再帰的にアップロード、ダウンロードできます。

    $ aws s3 cp myfolder s3://mybucket/myfolder --recursive
    upload: myfolder/file1.txt to s3://mybucket/myfolder/file1.txt
    upload: myfolder/subfolder/file1.txt to s3://mybucket/myfolder/subfolder/file1.txt

    $ aws s3 cp --acl public-read myfolder s3://mybucket/myfolder
    

これで見れる

#6-4 cloudFront

ＡＭＩでインスタンスを立ち上げる

CloudFrontに行き「Create Distribution」をクリック

Webの「Get Started」を選択

Origin Domain Nameに立ち上げたインスタンスのパブリックDNSを入力

Create Distributionをクリック

ちょっと待つ

ブラウザでhttp://Domain name(CloudFrontのやつ）を実行

Welcome to AMIMOTO AMIと出るのでインスタンスＩＤを入力

成功！！ラッキー

#6-5 ELB

AMIを使ってインスタンスを２つ作成

ロードバランサーをクリック

ロードバランサーの作成

ロードバランサー名に学籍番号を入力→　次の手順へ

セキュリティグループの割り当てで既存のセキュリティグループを選択するをチェックし自分のセキュリティグループをチェック

ec2に接続し/var/log/nginx/access.logでヘルスチェックを確認

ロードバランサーの説明タブのＤＮＳ名をブラウザで入力

ワードプレスが表示されたら完了



