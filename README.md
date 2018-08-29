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

### ドキュメント環境構築
```bash
cd /vagrant
curl -s api.sdkman.io | bash
source "/home/vagrant/.sdkman/bin/sdkman-init.sh"
sdk list maven
sdk use maven 3.5.4
sdk list java
sdk use java 8.0.181-zulu
sdk list gradle
sdk use gradle 4.9
```
ドキュメントのセットアップ
```
cd /vagrant/
touch build.gradle
```
`build.gradle`を作成して以下のコマンドを実行
```
gradle build
```
ドキュメントの生成
```bash
gradle asciidoctor
gradle livereload
```
[http://192.168.33.10:35729/](http://192.168.33.10:35729/)に接続して確認する


### パイプラインの構築
```
cd /vagrant/ops/code_pipline
./create_stack.sh 
```

**[⬆ back to top](#構成)**

## 配置
### AWS認証設定
```bash
cd /vagrant/sam-app
cat <<EOF > .env
#!/usr/bin/env bash
export AWS_ACCESS_KEY_ID=xxxxxxxxxxxx
export AWS_SECRET_ACCESS_KEY=xxxxxxxxxx
export AWS_DEFAULT_REGION=us-east-1
EOF
```
アクセスキーを設定したら以下の操作をする
```bash
source .env
aws ec2 describe-regions
```

### デプロイ
デプロイ用のS3バケットを用意する
```bash
aws s3 mb s3://go-hands-on
```
デプロイを実行する
````bash
cd /vagrant/sam-app
sam validate
make deps clean build
sam package --template-file template.yaml --s3-bucket go-hands-on --output-template-file packaged.yaml
sam deploy --template-file packaged.yaml --stack-name go-hands-on-development --capabilities CAPABILITY_IAM
````
デプロイが成功したら動作を確認する
```bash
aws cloudformation describe-stacks --stack-name go-hands-on-development --query 'Stacks[].Outputs[1]'
```

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


### Lintの実行
```
cd /vagrant/sam-app
go vet ./hello-world
```

### コードカバレッジの実行
```
cd /vagrant/sam-app
go test -coverprofile=cover.out ./hello-world/
go tool cover -html=cover.out -o cover.html
python -m SimpleHTTPServer
```
[http://192.168.33.10:8000/](http://192.168.33.10:8000/)に接続して確認する

**[⬆ back to top](#構成)**

# 参照 #
+ [Go Version Management: goenv](https://github.com/syndbg/goenv)
+ [Command vet ](https://golang.org/cmd/vet/)
+ [図入りのAsciiDoc記述からPDFを生成する環境をGradleで簡単に用意する](https://qiita.com/tokumoto/items/d37ab3de5bdbee307769) 