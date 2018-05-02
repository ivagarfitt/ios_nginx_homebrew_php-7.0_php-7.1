# Mac iOS + Nginx + Homebrew + php@7.0 + php@7.1
How To create local env using Mac iOS + Nginx + Homebrew + php@7.0 + php@7.1

Due to Homebrew/php tap being deprecated at the end of March 2018, and the moving of all PHP formulas to Homebrew/core I was struggling to find a tutorial that will explain how to create local environment with all this changes.

## 1. Install Xcode
If you don't have XCode installed, do this first because it will be used by Homebrew
```
$ xcode-select --install
```

## Install Homebrew
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Follow terminal prompts and after is finished the easiest way to make sure all is good is to type:
```
$ brew -v
Homebrew 1.6.2
Homebrew/homebrew-core (git revision efb00; last commit 2018-04-27)
```

Just to be sure run:
```
$ brew doctor
```
It will instruct you how to fix potential problems. 
**Note** **
