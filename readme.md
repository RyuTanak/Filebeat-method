# Filebeatの使いかた(1章)  
業務で得た知識を含め、Filebeatの使いかたを記載していく。  
## 目次  
[Elastic Stackについて](#content1)  
[Filebeatの概要](#content2)  
[インストール方法](#content3)  
[設定ファイル](#content4)  
[設定ファイルのフォルダ構成](#content5)  
[「データ取得」設定](#content6)  
[「データ送信」設定](#content7)  
[動作確認①（ファイル→ファイル）](#content8)  
[動作確認②（ファイル→Elasticsearch）](#content9)
[動作確認③（ファイル→Elasticsearch(index指定)）](#content9)

<h2 id="content1">Elastic Stackについて</h2>  
Elastic社が提供しているプロダクトは大きく4つ  

- Elasticsearch  
- Kibana  
- Logstash  
- Beats  

これらをElasticStackという  
それぞれを簡単説明すると  
- Elasticsearch  
  全文検索エンジン  
  DBサーバのイメージ。データ操作はKibanaで行う。  
- Kibana  
  Elasticsearchに登録されたデータをGUIでいじれる。  
  データを加工出来たり、グラフ表示出来たり、いろいろできる。  
- logstash  
  データの取得、加工、送信を行えるもの。    
  詳しくは別レジストリで→  
- Beats  
  Beatsファミリーと言われるものが、全部で7種類ある。（その中にFilebeatがある）  
  詳しくは公式サイト→https://www.elastic.co/jp/beats/  
  logstashで言うところの、データの取得・送信に特化した機能であり  
  logstashは何でもできる→1機能として重たい→データ取得・送信に特化したFilebeatが誕生した（らしい）。  

<h2 id="content2">Filebeatの概要</h2>  
上記の説明でFilebeatはデータの取得・送信に特化した機能と説明したが  
データの加工も行うことができる。そのことについては追々説明を行う。  

Filebeatの機能は大きく3つある  
- データ取得  
- データ加工  
- データ送信  

それぞれどう行っていくのか、使いかたについて説明していく。  
また、「データ」とは、ログのデータのことを指す（業務でログデータを扱っていたため）。

<h2 id="content3">インストール方法</h2>  

インストール方法といっても、様々な方法がある。[（公式リファレンス）](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation-configuration.html)   
また、WindowsやLinuxなど様々なOSにインストールもできる  
(Filebeatの参考サイトを見ていると、LinuxでFilebeatを使っているのをよく見る)  
今回はCentOS（Linux）へのインストールを行う。  

<h2 id="content4">設定ファイル</h2>  

インストールを行うと、/etc/filebeat/フォルダ下にfilebeat.ymlがある。  
基本的に、「データ取得」「データ送信」の設定はこのファイルで行う。  
基本的にはであるが、Filebeat.ymlで設定できることは「データ取得」「データ送信」に限らずめちゃめちゃある→https://www.elastic.co/guide/en/beats/filebeat/current/configuring-howto-filebeat.html  

ありすぎて説明しきれないので、ひとまず「データ取得」「データ送信」に絞って説明する。  

<h2 id="content5">設定ファイルのフォルダ構成</h2>  

Filebeatの各設定ファイルは以下の構成になっています。  
(※LinuxとWindowsで構成が異なります。今回はLinuxのフォルダ構成を示します)  

├etc  
│└filebeat  
│　├filebeat.yml  
│　└modules.d  
│　　├activemq.yml.disabled  
│　　├・・・  
├var  
│└log  
│　└filebeat  
├usr  
│└share  
│　└filebeat  
│　　└module  
│　　　└・・・

この章では/etc/filebeat/filebeat.ymlファイルしか使いません。 


<h2 id="content6">「データ取得」設定</h2>  

filebeat.ymlのfilebeat.inputs部分が「データ取得」に関する設定である  
filebeat.ymlのデフォルトは以下のようになっている。  
（コメント部分が多いため、コメント部分を省略する）  

```yaml
filebeat.inputs:

- type: filestream

  # Unique ID among all inputs, an ID is required.
  id: my-filestream-id

  # Change to true to enable this input configuration.
  enabled: false

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /var/log/*.log
    #- c:\programdata\elasticsearch\logs\*
```

上記の説明があるように、「filebeat.inputs」部分でデータ取得に関する設定を行う。  

「type: filestream」で入力になるものを決める。  
filestreamの場合は、指定したフォルダから指定したファイルを取得する設定になる。  
その下の「my-filestream-id」「enabled: false」「paths:」の設定はfilestream特有の設定になる。
https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-filestream.html  

filestream以外にもtypeで指定できるものは色々ある。  
https://www.elastic.co/guide/en/beats/filebeat/current/configuration-filebeat-options.html  
例えば、AWSのS3を入力に出来たり、HTTPのRESTが入力になったり、、、（私がfilestreamとlogしか試せてない）  

「id: my-filestream-id」は、リファレンスの説明によると以下の通り。  
```
入力IDを変更すると、ファイルの状態が失われ、最初から読み取られるため、データが重複する可能性があります。
```
ログをどこまで読み込んだかを記録するために必要なID？  
「my-filestream-id」はなんでもいいっぽい  

「enable: false」はこのfilestreamの設定を使うか使わないかの設定  
falseのままだとこの設定は読み込まれない  

「paths:」部分に、具体的な、「指定したフォルダから指定したファイルを取得する」の設定をする  
/var/log/*.log←だと/var/logフォルダ下のファイル名が.logで終わるファイルを読み込む。という意味になる  

上記で説明した「id」「enable」「path」はfilestreamのオプション設定といい  
このオプション設定項目は他にも様々ある。詳しくは公式リファレンスを参照。  

<h2 id="content7">「データ送信」設定</h2>  

filebeat.ymlのfilebeat.outputs部分が「データ取得」に関する設定である  
filebeat.ymlのデフォルトは以下のようになっている。  
（コメント部分が多いため、コメント部分を省略する）  

```yaml
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["localhost:9200"]

  # Protocol - either `http` (default) or `https`.
  #protocol: "https"

  # Authentication credentials - either API key or username/password.
  #api_key: "id:api_key"
  #username: "elastic"
  #password: "changeme"
```

「output.elasticsearch」でElasticsearchへの出力を指定します。  
このoutput.~~は何種類かあり、output.fileとすれば、ローカルファイルにデータを落とすこともできます。  
その他種類・詳細は→https://www.elastic.co/guide/en/beats/filebeat/master/configuring-output.html  

「hosts: ["localhost:9200"]」で、Elasticserchの接続先を指定します。  
"接続先アドレス:ポート"で書きます。接続策アドレスは、ElasticsearchのエンドポイントのURLでも指定可能です。  
ポートの9200はElasticsearchのデフォルトで設定されているモノです。  

「protocol: "https」はElasticsearchへ接続するときの通信方式を指定します。  
デフォルトはhttpです。  
httpsであれば、その下の「#api_key: "id:api_key"」などを指定してください。  
「username: "elastic"」「password: "changeme"」はElasticsearchのユーザ名とパスワードです。  

output.elasticsearchのリファレンス→https://www.elastic.co/guide/en/beats/filebeat/master/elasticsearch-output.html  


<h2 id="content8">動作確認①（ファイル→ファイル）</h2>  

実際にFilebeatを動かしてみましょう。  
まずは簡単なところから。  
指定したログファイル→指定したファイルに落とすという動作確認です。  
filebeat.ymlに以下の設定をします。  

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/*.log
output.file:
  path: "/tmp/filebeat"
  filename: filebeat
```
ファイルを編集した後、サービスの再起動を行う。  
```
sudo systemctl restart filebeat.service
```
設定ファイルの記述が正しいかコマンドでチェックできる。  
```
sudo filebeat test config
```
/var/logフォルダ下にtest1.logを配置する。  
テキストの中身は自由に記述。  
filebeatはデフォルトで1秒周期で起動するため、ファイルを置いたら  
/tmp/filebeatフォルダ下にfilebeatテキストが出力される。  

filebeatのコマンド操作については、以下の公式リファレンスに乗っている。  
https://www.elastic.co/guide/en/beats/filebeat/7.17/command-line-options.html  

<h2 id="content9">動作確認②（ファイル→Elasticsearch）</h2>  

filebeat.ymlに以下の設定をします。  
※前提として、同じネットワーク内にElasticsearchがあること  

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/*.log
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "filebeat_writer"
  password: "YOUR_PASSWORD"
```
登録先のindexを指定していないため、index名はデフォルト値になります。  
デフォルト値：filebeat-7.17.7-2022-11-03-000001  


<h2 id="content10">動作確認③（ファイル→Elasticsearch）</h2>  

動作確認②に加え、データ登録先のindexを指定する方法を示します。  

filebeat.ymlに以下の設定をします。  

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/*.log
setup.template.name: "filebeat"
setup.template.pattern: "filebeat-*"
setup.ilm.enabled: false
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "filebeat_writer"
  password: "YOUR_PASSWORD"
  index: "filebeat-index"
```
「filebeat-index」というindexにデータが登録される。  


