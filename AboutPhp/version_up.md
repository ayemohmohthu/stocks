
### Homebrewコマンドを使って現在インストールができるPHPのバージョンを検索
 - brew search php@7

### アップしたいVersionのphpインストール
 - brew install php@7.4

### PATH通す
 - echo 'export PATH="/usr/local/opt/php@7.4/bin:$PATH"' >> ~/.bash_profile
 - echo 'export PATH="/usr/local/opt/php@7.4/sbin:$PATH"' >> ~/.bash_profile

### PHP再スタート
 - brew services start php@7.4

### ターミナルを閉じる

### version確認
 - php -v