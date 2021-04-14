## libpq-devとは
```
・header files for libpq5 (PostgreSQL library)
```
## npm とは
```
・Node Package Managerの略で、Node.jsのモジュールを管理するツール。  
・npmでインストールしたパッケージのバージョン情報をpackage.jsonに格納します。  
・このpackage.jsonからパッケージを一括でインストールすることが出来るため、package.jsonを一元管理することによりフロントエンドで使用するパッケージのバージョン管理を一元化することが出来ます。  
・Node.jsをインストールすると同時にnpmもインストールされます。  
 ・npm init ：初期化  
 ・npm install : パッケージの復元  
 ・npm install jquery : パッケージのインストール  
 ・npm update : パッケージの更新、など。
```
## yarn とは
```
・npmと互換性のあるパッケージマネージャー  
・homebrewを使っているのであれば、homebrewからインストールできる。    
　brew install yarn  
 ・メリット  
  ①npmと一緒に使える  
  ②コマンドの実行が簡単  
  ③学習コストがすごく低い  
  ④めっちゃ早い  
・詳しいは別ファイル参考👇  
  ＜yarnについて＞
```

## docker-compose buildする際にdocker-compose command not found
```
👉dockerをインストールした時、brew install dockerで単純にdockerのみをインストールしてしまったため。  
👉brew cask install dockerでインストールしたら、dockerもdocker-composeもdocker-machineもインストールされるのが知らなかったため。  
👉今回はbrew install docker-compose,brew install docker-machineでそれぞれをインストールした。  
👉そして、もう一回docker-compose buildしたら、ERROR: Couldn't connect to Docker daemon - you might need to run `docker-machine start default`.  
👉docker-machine start defaultを実行したら、Docker machine "default" does not exist. Use "docker-machine ls" to list machines. Use "docker-machine create" to add a new one.  
👉Error with pre-create check: "VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path"  
👉brew cask install virtualboxでVirtualBoxインストール  
👉最後の結論  
　dockerをhomebrewからインストールする時、  
   　❌ brew install docker  
   　⭕️ brew cask install docker  
```

## docker作成
```
   ①docker-compose run --rm web rails new . --force --database=mysql --skip-bundle  
   ②docker-compose build
```

## db接続してdocker起動
```
出来上がったappの下のconfig/database.ymlを編集  
docker-compose up  
アプリもDBも同時に起動される。  
確認は docker ps -a で
```

## db作成
```
docker-compose run --rm web rake db:create  
```
## appを変更したい場合
```
docker-compose down  
 変更開始
   docker-compose run --rm web rails g scaffold item name:string amount:integer memo:text  
   docker-compose run --rm web rake db:migrate   
 変更終了
docker-compose up -d
```
##### docker-compose upしたままviewやcontrolerを弄っても(いじっても)ブラウザ上に反映されることを確認。

## MySQLViewerとしてTerminalで見るときは  
```
docker psでMySQLが動いているContainerを確認して、以下のコマンドでMySQL　DBサーバをみれる。
　docker exec -it <'MySQLContainerID'> bash  
  　　mysql -u root -p  
  　　⇨<'password'>  
```   
## WebAppContainerでもDBサーバが見れる。
```
docker exec -it <'WebContainerID'> bash  
 ・rails c  
 　👉 Modelを見れる。  
      例：Item.all　　　　　　　　　　　　　　　　　　←検索  
    　item = Item.create(name: "aaaa") 　　　　　←登録  
・rails dbconsole  
  👉 DBViewer  
   例：show databases;  SHOW COLUMNS FROM City;  
       use <'DB NAME'>    
  　　 select / update / delete / insertなどSQL queryを実行される。  
```

## 新しいGemのインストール後には、docker-compose buildを実行してから、docker-compose upを実行するように！！
