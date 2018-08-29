AWS SAM Go Hands-on
===================
# 目的 #
AWS サーバーレスアプリケーションモデル (AWS SAM) ハンズオン(Go) 

# 前提 #
| ソフトウェア   | バージョン   | 備考        |
|:---------------|:-------------|:------------|
| golang         |1.11.0    |             |
| sam            |0.5.0  |             |
| docker         |17.06.2  |             |
| docker-compose |1.21.0  |             |
| vagrant        |2.0.3  |             |

# 構成 #
1. [構築](#構築 )
1. [配置](#配置 )
1. [運用](#運用 )
1. [開発](#開発 )

## 構築
### 開発用仮想マシンの起動・プロビジョニング
+ Dockerのインストール
+ docker-composeのインストール
+ pipのインストール
```bash
vagrant up
vagrant ssh
```

### 開発パッケージのインストール
+ aws-sam-cliのインストール
+ goenvのインストール
+ goのインストール
```bash
pip install --user aws-sam-cli==0.5.0
git clone https://github.com/syndbg/goenv.git ~/.goenv
echo 'export GOENV_ROOT="$HOME/.goenv"' >> ~/.bash_profile
echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(goenv init -)"' >> ~/.bash_profile
exec $SHELL
souce ~/.bash_profile
goenv install 1.11.0
```

### ローカルでテストする
```bash
cd /vagrant/sam-app
make deps
go test -v ./hello-world
make clean build
sam local generate-event api > event_file.json
sam local invoke HelloWorldFunction --event event_file.json
sam local start-api --host 0.0.0.0
```
[http://192.168.33.10:3000/hello](http://192.168.33.10:3000/hello)に接続して確認する


**[⬆ back to top](#構成)**

## 配置
**[⬆ back to top](#構成)**

## 運用
**[⬆ back to top](#構成)**

## 開発
### アプリケーションの作成
```bash
cd /vagrant
sam init --runtime go
cd sam-app
```

### Lintの実行
```
cd /vagrant/sam-app
go vet ./hello-world
```


**[⬆ back to top](#構成)**

# 参照 #
+ [Go Version Management: goenv](https://github.com/syndbg/goenv)
+ [Command vet ](https://golang.org/cmd/vet/)