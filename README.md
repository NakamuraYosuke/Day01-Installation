# Day01 -OpenShift（CRC）の導入-
## CRCインストール
### CRCとは
CRCはCodeReady Containersの略称です。
OpenShift version 4をシングルノードVM環境にデプロイするツールです。
Kubernetesユーザであれば、Minikubeというシングルノード環境をデプロイするツールがありますが、そのOpenShift4版という位置付けです。

MinikubeのようなことをするOpenShift用ツールは、以前はMinishiftがあったのですが、3.11で対応が終了してしまったらしいです。
4.x以降はCodeReady Containersを使うようになりました。

### CRCの種類
CRCには2つの種類があります。
    - Red Hat CodeReady Containers（Red Hat CRC）
    - CodeReady Containers for OKD 4（CRC for OKD）
勉強会ではRed Hat CRCを使用します。

### CRCの入手方法
RedHat Developerのサイトからダウンロードします。
ダウンロードの際、RedHatアカウントが必要となります。
![](https://raw.githubusercontent.com/NakamuraYosuke/Day01-Installation/master/images/download.png)

自身のインストール環境のOS種類を選択し、「Download CodeReady Containers」をクリックする。

次に、「Download pull secret」からシークレットをダウンロードする。
このシークレットは、インストールの際に必要となります。

ダウンロードが終わったら、資材（crc-macos-amd64.tar.xz ）を解凍し、実行ファイルをパスが通っているディレクトリへ移動しセットアップします。

```
$ tar xzvf crc-macos-amd64.tar.xz
$ sudo mv crc-macos-1.22.0-amd64/crc /usr/local/bin
$ crc setup
```

セットアップが完了すると下記のメッセージが表示されます。
`Setup is complete, you can now run 'crc start' to start the OpenShift cluster`

### CRCの起動
ターミナルに下記コマンドを入力します。
pull-secret.txtは先ほどダウンロードしたファイルを指定してください。
```
$ crc start -p pull-secret.txt
```

起動完了時、下記の情報がターミナルに出力されます。
```
To access the cluster, first set up your environment by following the instructions returned by executing 'crc oc-env'.
Then you can access your cluster by running 'oc login -u developer -p developer https://api.crc.testing:6443'.
To login as a cluster admin, run 'oc login -u kubeadmin -p <password> https://api.crc.testing:6443'.
```
### 環境変数の設定
下記コマンドを入力します。
```
$ eval $(crc oc-env)
```

### コンソール画面の起動
コンソールが正しく表示されるかを確認します。
```
$ crc console
```
ブラウザが起動し、crcコンソール画面が表示されます。
`kube:admin`を選択し、アカウント名は`kubeadmin`、パスワードは上記で出力された`<password>`を入力します。

下記のダッシュボードが表示されればOKです。
![](https://raw.githubusercontent.com/NakamuraYosuke/Day01-Installation/master/images/crcconsole.png)

### CRCターミナルへのログイン
開発者ユーザ（developer）でログインします。
```
$ oc login -u developer -p developer
```
ログインできれば下記のようなメッセージが表示されます。
```
Login successful.

You don't have any projects. You can try to create a new project, by running
```

管理者ユーザ（kubeadmin）でログインします。
```
$ oc login -u kubeadmin -p <password>
```
ログインできれば下記のようなメッセージが表示されます。
```
Login successful.

You have access to 58 projects, the list has been suppressed. You can list all projects with ' projects'

Using project "default".
```
試しにプロジェクト一覧を表示してみます。
```
$ oc get project
```
下記のようなプロジェクトの一覧が表示されます。
```
NAME                                               DISPLAY NAME   STATUS
default                                                           Active
kube-node-lease                                                   Active
kube-public                                                       Active
kube-system                                                       Active
・・・
```
なお、ocコマンドはOpenShift独自のコマンドですが、Kubernetesで利用するkubectlでは下記となります。
```
$ kubectl get project
```
こちらも同様の結果が出力されます。

CRCの導入は以上で終了です。

## Homebrewのインストール
### Homebrewとは？
最近ではOSや言語毎にパケージマネージャとよばれるツールが提供され、ソフトウエアや拡張機能のインストールはパッケージマネージャを利用して行うことが一般的になってきています。
HomebrewはMacOS環境におけるいわゆるデファクトスタンダードなパッケージマネージャです。

勉強会で使用するgitやdockerはHomebrewでインストールします。

### インストール方法
ターミナルを起動します。
下記スクリプトを貼り付けてください。
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```
続けるにはEnterを押す。パスワードを聞いてくるのでログイン時のパスワードを入力します。
Xcodeのcommand linet toolが必要になるが、自動インストールされます。

インストールが進み、[Installation Success]と表示されたらインストール完了です。
![](https://raw.githubusercontent.com/NakamuraYosuke/Day01-Installation/master/images/homebrew.png)

## Gitのインストール
勉強会で扱う資材はGitHubに格納されています。
これらの資材を使うには、Gitを利用してご自身の端末で動かすようセッティングする必要があります。

### インストール方法
Homebrewからインストールします。
```
$ brew install git
```
Gitの使い方は次回説明します。

## Dockerのインストール
自身で作成したコンテナイメージをOpenShift（CRC）で動かすには、
コンテナイメージをビルドしたりイメージレジストリへpushする必要があります。
それを行うのがDockerです。（Podmanでも良いです）

### インストール方法
Homebrewからインストールします。
```
$ brew install docker
```
