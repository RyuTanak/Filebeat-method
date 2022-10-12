# Filebeatの使いかた  
業務で得た知識を含め、Filebeatの使いかたを記載していく  
## 目次  
- [ElasticStackについて](#content1)  
- [Filebeatの概要](#content2)  
- [インストール方法](#content3)  
- [設定ファイル](#content4)  

<h2 id="content1">ElasticStackについて</h2>  
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
インストール方法といっても、様々な方法がある。

[（公式リファレンス）](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation-configuration.html)   

また、WindowsやLinuxなど様々なOSにインストールもできる  
(Filebeatの参考サイトを見ていると、LinuxでFilebeatを使っているのをよく見る)  
今回はCentOS（Linux）へのインストールを行う。  
※VMWareで用意する予定であるが、自分のノートPCのメモリが8Gのため、厳しい、、、  

<h2 id="content4">設定ファイル</h2>  
インストールを行うと、/etc/filebeat/フォルダ下にfilebeat.ymlがある。  
基本的に、「データ取得」「データ送信」の設定はこのファイルで行う。  

基本的にはであるが、Filebeat.ymlで設定できることは「データ取得」「データ送信」に限らずめちゃめちゃある→https://www.elastic.co/guide/en/beats/filebeat/current/configuring-howto-filebeat.html  

ありすぎて、説明しきれないので、ひとまず「データ取得」「データ送信」に絞って説明する。  




