# Installation of php@7.2 on Mac with homebrew
## install homebrew if it haven't installed yet
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
## install php
```
brew install php@7.2
```

## When Homebrew error about command line tools occurs
```
brew cleanup
brew upgrade
xcode-select --install
```

## Install the required PHP to your PATH
```
echo 'export PATH="/usr/local/opt/php@7.2/bin:$PATH"' >> ~/.bash_profile
echo 'export PATH="/usr/local/opt/php@7.2/sbin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
```

## version check
```
php -v
php -version
```

# composer installation
## install openssl
```
brew install openssl
```

## composer download
[download composer](https://getcomposer.org/download/)

## path setting
```
sudo mv composer.phar /usr/local/bin/composer
```

## version check
```
composer --version
```