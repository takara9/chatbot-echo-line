# オウム返しするチャットボット LINE版

LINEアプリから送信されたメッセージをオウム返しするもので、チャットボット開発のベースとして作りました。

## Bluemix CFアプリとしてデプロイする場合
パソコンなど作業用の環境で以下のコマンドでファイルをダウンロードします。

~~~
git clone https://github.com/takara9/chatbot-echo-line
~~~
* credential.json.sample を credential.json にリネームします。
* LINE BUSINESS CENTER から accessToken, channelSecret を取得して credential.json に設定します。参考資料(1)
* HTTPSの暗号化通信は、 Bluemix のプラットフォームが変換するので credential.json から、これに関する部分を削除します。

~~~
{
    "accessToken": "put here your access token",
    "channelSecret": "put here your channel secret"
}
~~~

* Bluemix CLI コマンドをインストールして、Bluemix にログインします。 参考資料(2)
* 以下のコマンドを使って、CFアプリとしてデプロイします。 このコマンドは manifest.yml を読み込んでデプロイを実行するのですが、host が同一ドメインに既に存在すると失敗します。その場合は manifest.yml を編集して再実行します。

~~~
bx cf push
~~~

* コマンドが完了したら、Bluemix ポータルのダッシュボードのアプリ名 line-chatbot に、URLが表示されています。このアプリのURLをLINE developer の Message API の Webhook URLに設定して、Verify を実行して Success と表示されれば、準備完了です。
* LINEアプリからチャットボットへメッセージして、同じメッセージが受信できれば完了です。


## Bluemix Infrastructure の仮想サーバーにデプロイする場合

* Bluemix Infrastructure の仮想サーバーを最小条件で起動します。(CPUコア:1, RAM: 1GB, NIC: 100Mbps, Disk: local 25GB) 参考資料(3)
* サーバーに次のコマンドでダウンロードします。

~~~
git clone https://github.com/takara9/chatbot-echo-line
~~~


* この仮想サーバーは、HTTPSの暗号化 をサーバーのアプリに実装する必要があります。デジタル証明書取得のために、仮想サーバーのIPアドレスをドメイン名で参照できる様にします。参考資料(4)
* HTTPS 通信のためのデジタル証明書を取得して、下記の key と cert にPEM形式のファイル名を設定します。 参考資料(5)
* LINE の accessToken, channelSecret を取得して、credential.json の下記に部分に設定します。

~~~
{
    "accessToken": "put here your access token",
    "channelSecret": "put here your channel secret",
    "port": 3000, 
    "https": {
        "key":  "lets_encript.key",
        "cert": "lets_encript_fullchain.crt"
    }
}
~~~
* 必要なファイルをインストールします。

~~~
npm install
~~~
* 次にアプリをスタートさせます。

~~~
npm start
~~~

* LINE developer の Message API の Webhook URLに、仮想サーバーのドメイン名で https://ドメイン名:3000/webhock 登録して接続の確認を行います。Success と表示されれば、準備完了です。
* LINEアプリからチャットボットへメッセージして、同じメッセージが受信できれば完了です。



## 参考資料
- (1) LINE BOTの作り方を世界一わかりやすく解説（１）【アカウント準備編】http://qiita.com/yoshizaki_kkgk/items/bd4277d3943200beab26
- (2) Bluemix CLIホーム https://clis.ng.bluemix.net/ui/home.html
- (3) CHANGE MAKERS 1.2 仮想サーバーを起動するには？ https://www.change-makers.jp/docs/softlayer-config-guide/10292
- (4) 私的MyDNS.JP https://www.mydns.jp/
- (5) Let's Encrypt 総合ポータル https://letsencrypt.jp/
- (6) line-msg-api https://www.npmjs.com/package/line-msg-api

