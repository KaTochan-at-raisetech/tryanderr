# Local by Flywheel（win版）がうまく動作しない時のWordPress環境構築の一例（ver0．97）
# 結論：WSL2 & Docker for Windows (KUSANAGI Runs on Docker) で環境構築が可能でした。  
（2020/12/09　WP5.6-jaの動作しました。）   

## (注意)内容の信憑性には、常に疑問を持ちながらお読みください。
## 内容は、Local by Flywheel（win版）が動かず開発環境に困っている方を対象に、また自身の備忘録をかねて記載しました。
## ベースの環境さえできれば(手間がかかります)、⑤以下の、＄や＃を除いて、一行ずつコピペすることで再現できると思います。
## Vscodeでの、LiveSever拡張機能は、プロクシの設定を行ってもうまく動作しませんでした。（参考）https://ipsjk.nori2tae.com/178 。
## あきらめて、header部分に、
```shell
<meta http-equiv="refresh" content="2; URL=">
```
## を記載。2秒で自動的にリロードして代用しました。本番環境では、必ず削除！！

間違いなどありましたら、どうぞご指摘ください。また各ツールのインストールや使用方法などは、詳しく記載していません。
WSL2、Docker、エディタ　nanoやviの使い方について、先にある程度、情報を入れておいてください。
# WSL2（shell）が、操作＆理解できないと、厳しいです。WSL2上（ubuntu）で、パスを通す作業があります。
またエディタは、VScodeも、WSL2上のコマンドラインから、code ファイル名 で使用できます。
また記事の内容で、利益相反はありません。

---
# 概要
このレポートはネットから得た情報で、WSL2環境 & Docker for Windowsで、WordPressの学習環境が出来ましたという内容です。
＝　検索の時間の節約、足がかり（検索文言の参考）には、できると思います。
また表題がレポートの全てで、それ以上の情報はありません。授業で推奨される、Local by Flywheel（win版）がうまく動作しない時の代替手段です。
ただし、この環境構築の経験は、本番環境へのデプロイに役立つと思います。

ベースとなるWSL2のセットアップに、労を要します。ただWSL2は、Winの癖を意識することなく、ネットのlinuxの記事が、ほぼそのまま利用できるメリットもあるかと思います。
またエラーが出た際、WSLの設定なのか、Dockerのコンテナの設定なのか、WPの設定なのか、構築ツール（KUSANAGI RoD）の設定なのか、切り分ける必要があります。それは、まさに沼です。

そうです。今が他の方法を考えるチャンスです。それでも、読み進めるなら。。。

---
# Dockerを使用するまでの経緯
きっかけは、自機(Win10)で、Local by Flywheel（win版）がうまく動作しなくなったからです。
当初は、Local by Flywheel は順調に機能していたのですが、いつの頃からか Uh-oh!と不安定に。
起動のプロセスがはっきり、調べられず、自力で解決に至りませんでした。
根拠はありませんが、不安定になる前後の作業から推察すると、VirtualBoxやWSL2の仮想環境インストール後からだと思います。
代替手段として、Dockerを利用して環境構築を行いました。
皆さんも、VirtualBoxやWSL2を、近いうちにセットアップする方だいるかもしれません。そんなとき、思い出してみてください。
ただ現時点（2020月11月）で、VirtualBox の使用時に Hyper-V が有効だと不安定でした。

## 気づき：Local by Flywheelが、動作しない場合は、仮想環境を含めて、意図しないローカルサーバーが動作していないか確認を。
自機でも、Local by FlywheelのNginxと、仮想環境のNginxと二重に起動していたことがありました。
Port 80と重なっていたため、動かないはずですね。この点は、Docker環境に切り替えても同じことが言えると思います。
終了したつもりでも、サーバが、ゾンビプロセス化していたこともありました。気づかない！
    参考:似た内容です。
    https://qiita.com/wasipo/items/660c4488774c544867dc

---
# 動作環境と環境構築ツール
## 動作環境
Windows 10 Pro, バージョン 2004, OS ビルド 19041.508  
WSL2 / ubuntu 20.04  
Docker for Windows (Docker Engine v19.03.13)  
(Dockerコマンドは、WSL2から利用しました。cmd や Power Shellからは、試していません。cmdは、WSL2の再起動には使います。)

## 環境構築ツール
Dockerイメージの構築サポートツールを使用しました。  
KUSANAGI Runs on Docker  
日本の会社がWordPressをカスタマイズしており、情報源が和文です！随時更新、改良されています。（設定が変わることも。。。）
参考:インストールなど  
https://kusanagi.tokyo/cloud/kusanagi-runs-on-docker/
また、公式に出ているDocker machineは、必須ではありません。
（使用することなく動作可能です。ローカルで考えると、複数のDocker環境環境を作るとき便利と思います。今回、試していません。）
参考記事:KUSANAGI RoDの使い方（1）～(10)の記事、ほか多数
keyword: RoD wp-config.php wpなど

---
# インストールからWordPress構築まで
## ①WSL2の環境構築。ネットで和文で十分な情報があると思います。
慣れないと大変です、時間がかかります。失敗するとWinの起動にも影響するかも。はじめに ***Backupをおすすめします！！！！！！。***
## ②Docker for Windows は、通常のWinソフトのインストールに同じ。WSL2での使用を考慮してオプションを選択。
特に苦労することなく、すんなりと出来ました。自動で始まるチュートリアルで動作確認もできました。
## ③以下のコマンドを、***WSL2にインスト-ル。whitch コマンドで確認できる状態に***
    bash (4.x以上)
    git
    sed
    awk
    grep
    gettext
    envsubst （envsubstはgettextのインストールで同時にインストールされました）
    curl
## ④その上でWSL2に、KUSANAGI Runs on Dockerをインストール。
公式RoDのページどうりです。

```Shell
$ curl https://raw.githubusercontent.com/prime-strategy/kusanagi-docker/master/install.sh | bash

```

KUSANAGI RoDコマンドの本体は、$HOME/.kusanagi/bin/kusanagi-docker となり、$HOME/.kusanagi/bin をshellのPATHに追加。（.bashrcなど）
動作確認は、$ kusanagi-docker --help

## ⑤WordPressの環境を作るための適当な作業Dirを作成して移動。kusanagi-dockerを実行（プロビジョン：配置？）。
以下の例は、home dirに、mydirを作って移動。kusanagi-dockerコマンドにてプロビジョン実行まで。
(余計なコンテナが動いていない状態で行ってください。必要なら　docker stop xxxxを。)

```Shell
$ cd
$ mkdir mydir
$ cd mydir
$ kusanagi-docker provision --wplang ja --fqdn wp.localhost kusajp
```

コマンドオプションは公式ページで確認してください。いろいろ可能です。  
mydirの中に、kusajp/のフォルダが作成され、その中に、docker-compose.ymlが作成されいると思います。
また、kusajp/ は、git管理に登録状態で作成されます。

設定内容は次のようにして確認できます。
WordPressログイン用のUSER名やPWを確認できます。
WordPressのダッシュボード→更新など使われるユーザー(kusanagi)のPWも確認できます。
まとめてデスクトップにでも、コピペしておきましょう。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose config | less

上下キーで操作できると思います。
 ...
 ADMIN_PASSWORD: EusKJ6I7KpU3MHcZqhxxxxx
 ADMIN_USER: QYfOxxxxxx
 ...
 KUSANAGIPASS: btRLLn7TnimcjUpxxxxxxxx


画面から出るときは、:q
```

では、次のようにしてdocker-compose.ymlある高さで、コンテナの起動してみましょう。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
```

エラーが出たり、一度で起動しないときは、停止、起動をもう一度してみてください。

```shell
$ docker-compose down
$ docker-compose up -d

```

たぶん、起動すると思います。しなければ、なにかのインストールがうまくうまくいっていない可能があります。
Win上のChromeなどで、http://wp.localhost/
またはlocalhostだけでも、接続できると思います。
表示された画面の右下のリンク、からログイン。（控えたADMIN_USERとADMIN_PASSWORDを使用）
一端、コンテナを終了しておきましょう。起動したままでは、次のdocker-compose.ymlの編集ができないため。です。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose down
```

また, プロビジョンで作成した、ターゲットディレクトリは、Gitでのバージョン管理が可能な状態で作成されています。
設定で変更する前後での、コミット、設定の異常が起こっても、いつでも戻れるように事前の準備をおすすめします。

webページが表示されない？？？などのエラー原因の切り分けにも役立ちます。
例えばこの時点までは動いていたので、コミット位置まで内容を戻す。（＝設定はおかしくない）
あれ？それでも起動しない、、、。あ、終了していないコンテナがある。。。などです。
（ただしローカル環境のコミット時と、永続化しなてい部分でコンテナの内容が乖離している可能性もなくはないかと）

## ⑥PHPコンテナのアップロード制限を解除
（注意）このあたりから内容に,だいぶ自信がありません。（注意）
PHPの初期設定では、ファイルのアップロード制限があるので、ある程度解除します。
（各自で必要に応じて調整を、先を見据えてデプロイ先のサーバに合わせるなど）
初期設定のままでは、自作のテーマ、画像のアップができません。

大まかには、docker-compose.ymlのあるdirに、設定用のdirを作成、そのdirに、iniファイルを作成します。
この設定は、docker-cmposeで、コンテナを起動するとき反映されます。（永続化される）
設定ファイルを作成して、つづいてdocker-compose.ymlのファイルに、一行、追加します。

⑥-1 設定用のdirを作成、そのdirに、iniファイルを作成。

```Shell
$ cd $HOME/mydir/kusajp
$ mkdir php
$ cd $HOME/mydir/kusajp/php
$ touch uploads.ini
$ nano uploads.ini
- 以下のfile-600までの内容を記載。


file_uploads = On
memory_limit = 256M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 600


```

保存、終了。(最近のnanoなら 、 Ctrl-s 、Ctrl-ｘ)

⑥-2 docker-compose.ymlを編集。
ファイルに記載されている##CONFIGの php: の　volumes:内 に一行追加しますが、大事な点がひとつ。
編集の際はインデントの構造（スペースの数）を壊さないこと！。
ymlは、インデントを使い階層構造を表現します。（スペースの数に意味があります。）
またインデントにはタブが使えずスペースのみが使える。スペース2個単位でインデントすることが多い。

nanoではなく、vscodeで編集するなら、次のようにします。

```shell
$ cd $HOME/mydir/kusajp
$ code docker-compose.yml
```

```YAML
##　変更前　phpの中を編集
volumes:
  - database:/var/run/mysqld
  - kusanagi:/home/kusanagi

↓
##　変更後　phpの中を編集した
volumes:
  - database:/var/run/mysqld
  - kusanagi:/home/kusanagi
  - ./php/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini  #※追加
```

⑥-3 保存してファイル編集は終了。
記載した内容ですが、ローカルホスト上の　php/ ディレクトリを、docker-compose.ymlファイルからの相対パスで指定。
:で区切り、コンテナ内のマウントポイントを指定しました。

⑥-4 コンテナが起動するか動作確認。終了まで。
（上記のiniファイルが正しいDIRで、正しい内容で記載されていない場合や、docker-compose.ymlの文法、記載に違うところがあれば、起動しないと思います。確認してください。）

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
```

ブラウザで動作確認できたら、WordPressをログアウトして、コンテナも終了破棄。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose down
```

⑥-5 ターゲットディレクトリに移動してコミットまで。コミット前に、コンテナをいちお終了。（終了していたら、$ docker-compose downでエラーでますが、大丈夫です。）

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose down
$ git status
$ git add *
$ git commit -m "php設定と起動確認済み"
```

## ⑦ファイルがアップロードできるように、コンテナを設定する。
（注意）このあたりから内容に,だいぶ自信がありません。（注意）
RoDでは、ローカルのファイルを編集して、psuhコマンドでコンテナの内容を更新できます。
ただpushコマンドで、コンテナ内のパーミッションが変わようで、動作するようパーミッションを変更する必要がありました。
まずは、pushして、パーミッションを変更する例、本番は⑦-1から。

＃＃＃　ここは実行する必要はありません。＃＃＃  
```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker config push
$ docker-compose exec ftp sh -c "chmod 440 /home/kusanagi/kusajp/wp-config.php"
$ docker-compose exec ftp sh -c "chmod -R 777 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
```
＃＃＃　ここは実行する必要はありません。＃＃＃  

⑦-1 ローカルの編集ファイルを書き込み可能にする。（WordPress.org では「600」を推奨?）

```Shell
$ chmod 600 $HOME/mydir/kusajp/contents/wp-config.php
$ nano $HOME/mydir/kusajp/contents/wp-config.php
```

⑦-2 ローカルファイルの変更内容 。

```Shell
#define('FS_METHOD', 'ftpext'); #行頭に#をつけた
define('FS_METHOD', 'direct');  #この行を追加した

```
変更を保存まで。(最近のnanoなら 、 Ctrl-s 、Ctrl-ｘ)

⑦-3 書き込み先のコンテナを起動してから内容をpush。  
(注意)kusanagi-docker config pushは、コンテナ内のパーミッションの設定が変わるので、
chmodしなければ、WordPressがうまく動作にしません。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
$ kusanagi-docker config push
$ docker-compose exec ftp sh -c "chmod 440 /home/kusanagi/kusajp/wp-config.php"
$ docker-compose exec ftp sh -c "chmod -R 777 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
```

なおpushのあと、エラーがでますが、書き込みは成功しています。

```Shell
Creating kusajp_config_run ... done
Creating kusajp_config_run ... done
Creating kusajp_config_run ... done
Creating kusajp_config_run ... done
chown: unknown user/group kusanagi:www
ERROR: 失敗しました
```

⑦-4 気になるようであれば、コンテナ内の更新状況を確認まで。
下記のようにして、コンテナに入ることができます。
他のコンテナと違いftpのコンテナは、スーパーユーザになっています。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose exec ftp sh
 # cat /home/kusanagi/kusajp/wp-config.php |grep FS_
#define('FS_METHOD', 'ture');
define('FS_METHOD', 'direct');
 # ls -l /home/kusanagi/kusajp/wp-config.php
-r--r-----    1 kusanagi 1001          3403 Oct 14 02:37 /home/kusanagi/kusajp/wp-config.php

 # exit
 ```
 ftpコンテナをでます。

⑦-5 ターゲットディレクトリに移動してコミットまで。コミット前に、コンテナを終了。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose down
$ git status
$ git add *
$ git commit -m "wp-config.php設定まで済み"
```

＃＃＃　ここは実行する必要はありません。＃＃＃  
（参考）
⑦-3の作業は、コンテナに直接入って作業も可能です。 
docker-composeコマンドからコンテナの「サービス名を確認」して、サービス名を指定してコンテナに入れます。
（オプションの指定が、変わりますが、dockerコマンド直接でも可能）

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose exec ftp sh
# chmod 440 /home/kusanagi/kusajp/wp-config.php
```

＃＃＃　ここは実行する必要はありません。＃＃＃  



## ⑧docker-compose.ymlの修正（ローカルのテーマdirを、コンテナのテーマdirにマウント。）。
（注意）このあたりから内容に,だいぶ自信がありません。（注意）
ローカルの、/wp-content/themes/ ディレクトリと、コンテナ上の、/wp-content/themes/　と接続。
# もし別のdirを接続すると初期からの設定されているテーマ(Twenty Twenty, Twenty Nineteen, Twenty Seventeen)が消えます。
# ただ消えても、⑨ パーミッションの設定を行うと再インストールできます。

# (注意)テーマの適応でなどで、ログイン画面が消えているときは、直接、http://wp.localhost/wp-admin/
# (注意)あるいは、ログインのリンクが表示されるようなテーマをWP-CLI Commandsでインストールして対応。

データの永続化を行う。このフォルダ内で、テーマを作成します。
なお、#ローカルホスト上のthemes/ ディレクトリは、docker-compose.ymlファイルからの相対パスで指定。

⑧-1 docker-compose.ymlを編集。

FTPD ftp:　と  
CONFIG config: と php:  
services: httpd: と certbot:  
  
5カ所の全てのvolumes:に  

```shell
- ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分
```

また、編集の際はインデントの構造（スペースの数）を壊さないこと！。
ymlは、インデントを使い階層構造を表現する。またインデントにはタブが使えずスペースのみが使える。スペース2個単位でインデントすることが多い。

```Shell

$ cd $HOME/mydir/kusajp
$ docker-compose down
$ code docker-compose.yml

5カ所のうち一つ。## PHPのvolumes:内 に記述のした例だけ、あげておきます。のこり4つも変更してください。
##　変更前
volumes:
  - kusanagi:/home/kusanagi
  - ./php/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini  #※上記での追加部分

↓
##　変更後
volumes:
  - kusanagi:/home/kusanagi
  - ./php/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini  #※上記での追加部分
  - ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分
```

保存してファイル編集は終了。以上で、編集終わりです。編集終了後のdocker-compose.ymlを行末に添付しておきます。



⑧-3 コンテナを起動を確認。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
```

⑧-4 ターゲットディレクトリに移動してコミットまで。コミット前に、コンテナを終了。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose down
$ git status
$ git add *
$ git commit -m "初期設定終了、ローカルとコンテナのテーマdirを接続"
```

## ⑨ パーミッションの設定（セキュリティーの設定。今回は想定していませんが、サーバを公開するなら一番重要な項目です。）
# （注意）WordPressが動作しない時は、適宜、設定変更してください。
# （注意）WAN側からLAN内にアクセスできないような閉じた環境で、自分以外のアクセスに気を使わない条件での設定です。
# （注意）＝このままの設定で、サーバをネット公開できない内容です。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
$ docker-compose exec ftp sh -c "chmod -R 777 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
```

以下で、WordPressのダッシュボードのセキュリティーのエラーは、消えます。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
$ docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/"
$ docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
$ docker-compose exec ftp sh -c "chown kusanagi.kusanagi /home/kusanagi/kusajp/DocumentRoot/wp-content/"
```
  
（参考）https://www.atmarkit.co.jp/ait/articles/1702/06/news006_3.html  
（参考）https://www.nedia.ne.jp/blog/tech/2017/08/22/9188  
（参考）https://o9sec.com/2019/06/26/873/  


もし、テーマが正常に動作しない場合、次のようにして、テーマをインストールすることができます。
テーマ(Twenty Twenty）をインストール。wpコマンドを使っています。
```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker wp theme install twentytwenty --activate
```

# さあ、移動して、自作のテーマをがんがん開発していきましょう。
```Shell
cd $HOME/mydir/kusajp/contents/DocumentRoot/wp-content/themes/ 
```


## 情報提供 ⑩WordPress環境の削除には、コンテナの停止からイメージ削除まで以下のコマンドで一発です。
ホストOSを壊さない、docker環境の素晴らしさの一端と思います。
ターゲットディレクリで実行する必要があります。
（docker-compose.ymlファイルあるdirで、次のコマンドです。）

```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker remove

Removing network kusajp_default
WARNING: Network kusajp_default not found.
Removing volume kusajp_kusanagi
Removing volume kusajp_database
INFO: 完了しました。

```

ファイルが完全に削除できないときは、Docker for Windowsを再起動すると大抵削除できます。
下記のQ&A（ファイルが残るとき★）を参照。


## 情報提供 ⑪プロビジョン後の、起動、停止については通常のDockerの使用と同じだと思います。
Docker-compose とオプションの使用です。ターゲットディレクリで実行する必要があります。

```Shell
$ cd $HOME/mydir/kusajp

$ docker-compose up -d（起動させます）

$ docker-compose down（コンテナを破棄）
コンテナを破棄しますが、  volumes: を設定した部分は残ります。

$ kusanagi-docker remove（滅びの呪文)
イメージ、コンテナ、ネットワークをすべて削除するならこちら、バルスです。完全にさようならします。

$ docker-compose start または kusanagi-docker start (開始)
$ docker-compose stop または kusanagi-docker stop (停止)
$ docker-compose restart または kusanagi-docker restart (再起動)
```

## 情報提供　⑫WSL2の再起動( WSLを再起動して、Docker for Windowsも、最後にWin10も、再起動してください。)
設定を繰り返し、コンテナの再起動等繰り返していると、wsl2やdockerが不安定になります。
そのときは、全て再起起動することをおすすめします。

①WSL2の再起動。
win10で、管理者権限でcmdを開く、

```Shell
> net stop LxssManager（何度か入力することも）
LxssManager サービスを停止中です.
LxssManager サービスは正常に停止されました。
```

```Shell
> net start LxssManager
LxssManager サービスを開始します.
LxssManager サービスは正常に開始されました。
```

②つづいて鯨のアイコンを右クリックして、restartで、Docker for Windowsを再起動。

③その後、Win10の再起動をかけると、安心と思います。

---
# 考察
WSL2の環境とDocker for Windowsを整える条件下ですが、
Dockerイメージの構築をサポートする、KUSANAGI RoDを利用することで、
Dockerfileや docker-compose.ymlをゼロから作ることなく、WordPressの環境を構築できました。

上記で設定した、ローカルのテーマフォルダー内に、自作のテーマを作成して画像など表示してみてください。
コピーしたWordPressのテーマの、リンクを、テンプレートタグで置き換えて、動作を確認してみてください。

```shell
<img src="<?php bloginfo('template_directory'); ?>/image/～" alt="" />
```

またKUSANAGI RoDは、他のコンテンツ管理システム（Content Management System）の環境作成も可能です。Concrete5, Drupal7/8などです。
LAMP環境の構築（プロビジョン）も、好きなdirで、

```Shell
$ kusanagi-docker provision --lamp --fqdn lamp.localhost kusanagilamp
```

ワンライナーで、あっという間に作成、kusanagi-docker removeで削除一発です。
これをゼロから、自身で作成するのには、時間を要します。

またベースのDocker環境の構築ですが、WIN上ではなく仮想環境のWSL2上でも可能です。
Snapパッケージを利用すると簡単にインストール。Docker daemonも開始、停止も簡単ですが、
Win上のVisual Studio Codeと接続は、Docker for Windowsがスムーズにできました。
速度等の善し悪しはよくわかりません。
初学者は、Docker for Windowsが無難です。

```Shell
$ sudo snap install docker
$ sudo snap enable docker
$ sudo snap disable docker
```

今後、自機で運用するにもDocker環境のネットワークの設定が必要と思います。
（参考）
https://app.getpocket.com/read/2230046034
https://knowledge.sakura.ad.jp/23899/

また、コンテナその関係を理解することも、必要と思います。
（参考）
https://column.prime-strategy.co.jp/archives/column_3372
https://y2lab.org/blog/inet/customizing-wordpress-kusanagi-vol-1-5951/
https://qiita.com/yohm/items/047b2e68d008ebb0f001

以上、とりあえず、動きましたという、ところまで。

# まとめ
WSL2 & Docker for Windows (KUSANAGI Runs on Docker) でWordPressの環境構築が可能でした。
今回の設定は、閉じた環境を前提にレポートしました。セキュリティーの設定を改善する必要があります。
テーマ作成中に不都合がれば、設定を見直す必要があります。（たぶん、何度か必要になると思います。）

（参考）公式の中の人から、コメントがつくこともあるようです。
KUSANAGIユーザーグループ
https://users.kusanagi.tokyo/

# 謝辞
KUSANAGI RoD制作会社のプライム・ストラテジー株式会社の皆様に感謝です。

---
# Q&A、その他

（参考）
＃＃　WP-CLI Commands　＃＃
＃＃　テーマ周辺　＃＃
https://developer.wordpress.org/cli/commands/theme/

テーマをインストールして有効化（承認されているテーマなら、公式から直接ダウンロード、インストール可能）

```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker wp theme install twentytwenty --activate
```

ローカルからテーマzipファイルを使ったコマンドインストールが出来てないです。。。
（ダッシュボードからはできます。）

```Shell
$ kusanagi-docker wp theme install ../kusajp/cocoon-master-2.2.4.5.zip
```

しかし次のように、設定用コンテナ内からならできます。

```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose run --rm config sh
$ wp theme install cocoon-master-2.2.4.5.zip
```

ネットからインストールできるが、ローカルからのインストールができない。
ローカル、コンテナ間のuidの差異の問題？

削除

```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker wp theme list
$ kusanagi-docker wp theme list --status=inactive
$ kusanagi-docker wp theme delete twentyseventeen
```

最後のパラメーターは管理画面で目にするテーマの名前ではなく、テーマのフォルダー名です。

自作テーマで、ダッシュボードにログインすらできないとき、他の公式テーマをアクティブにしてログインできた★
（ｈttp://wp.localhost/wp-login.phpのように直接指定でもダッシュボードが動作しないとき）


テーマの有効化
```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker wp theme activate twentytwenty
```

マルチサイトが有効なら？

```Shell
$ cd $HOME/mydir/kusajp
$ kusanagi-docker wp theme enable twentysixteen
```



# $ kusanagi-docker remove ができないとき。！（ファイルが残るとき★）

手動で、ターゲットDirを削除。権限云々なら、sudoでおそらく強制削除できます。
ただ、Docker上に、ps(コンテナが起動したまま）が残っていると削除できません。（下記）

```Shell
$ sudo rm -fr /kusajp
rm: 'kusajp/php/uploads.ini' を削除できません: デバイスもしくはリソースがビジー状態です
rm: 'kusajp/themes' を削除できません: デバイスもしくはリソースがビジー
```

そのときは、docker psで起動しているコンテナIDを確認して、
docker stop してください。
さらにDocker for Windowsを右クリックで、restart。
これで、大抵は、消去できると思います

```shell
$ sudo rm -fr /kusajp
はい、できました。
```

# Vscodeで, Dcoker 拡張を使用して、コンテンやネットワークをストップ、削除できます。
# kusanagi-dockerのプロビジョンがうまく動作しないとき。
コマンドでも可能ですが、Vscode内から、コンテナやネットワークを削除出来ます。便利です。
コンテナとネットワークに不要なものがなければ、kusanagi-dockerのプロビジョンは成功すると思います。
それでもだめなら、イメージまで削除ですかね。

# chrome のキャッシュをクリア ( 説明つかない挙動のときに。なぜか動かないことがよくあります。)

```
ctrl+shift+del
Enter
ctrl+w
```
他にも、スーパーリロード、chromeで検索すると、3つほど、キャッシュをクリアする方法が見つかると思います。

# テーマのインストールと有効化
```shell
$ kusanagi-docker wp theme install twentytwenty --activate
ERROR: TARGET が指定されていません
```
docker-compose , kusanagi-docker　コマンドは、
プロビジョン先のターゲットディレクトリ（docker-compose.ymlのあるdir）で実行してください。


# 各コンテナの　id, cat /etc/passwd
```Shell
$ docker-compose exec php sh / docker-compose exec httpd sh
/var/www/html $ id
uid=1001(httpd) gid=1001(www)

/var/www/html $ cat /etc/passwd
root:x:0:0:root:/root:/bin/ash
httpd:x:1001:1001::/var/lib/www:/bin/nologin
kusanagi:x:1000:1000::/home/kusanagi:/bin/nologin
（省略）

$ docker-compose exec ftp sh
/ # id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)

/ # cat /etc/passwd
root:x:0:0:root:/root:/bin/ash
kusanagi:x:1000:1000:Linux User,,,:/home/kusanagi:/bin/false
（省略）


 docker-compose exec db sh
$ id
uid=999(mysql) gid=999(mysql) groups=999(mysql)
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
mysql:x:999:999::/home/mysql:/bin/sh
（省略)
```

---

# WordPress本体のupdateについて。（Kusanagi RoDのバージョンアップについて）


WPのダッシュボードから、WP本体の更新を試みましたが、権限エラーがでるため完了できませんでした。
今回は、WP-CLI Commandsから行いました。(下記のように本体更新後は、ダッシュボードから、プラグインの更新はできました。)

更新のやり方ですが、設定専用のdockerのコンテナに入り、適切なディレクトリに移動して、コマンドを実行します。


追記；アップデートの途中で、プラグインの更新が出来ないと警告がでるため、更新前にコンテナ内のパーミッションの設定をします。
（PHP Warning:　は完全に解消できていませんが、アップデートは可能です。）

```shell
まずは、コンテナ起動。
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
$ docker-compose exec ftp sh -c "chmod -R 777 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
$ docker-compose run --rm config sh
```

コンテナ内の所定のディレクトリ移動のうえ、現在バージョンの確認、アップデートの有無を確認。

```shell
$ cd /home/kusanagi/kusajp/DocumentRoot/
$ wp core version
5.5.1
$ wp core check-update
+---------+-------------+----------------------------------------------------------------+
| version | update_type | package_url                                                    |
+---------+-------------+----------------------------------------------------------------+
| 5.5.3   | minor       | https://downloads.wordpress.org/release/ja/wordpress-5.5.3.zip |
+---------+-------------+----------------------------------------------------------------+
```

5.5.1から、5.5.3 へ、アップデート実行。

```shell
$ wp core update

Updating to version 5.5.3 (ja)...
[05-Nov-2020 02:08:01 UTC] PHP Warning:  Declaration of WP_CLI\Core\CoreUpgrader::download_package($package, $check_signatures = true) should be compatible with WP_Upgrader::download_package($package, $check_signatures = false, $hook_extra = Array) in phar:///usr/local/bin/wp/vendor/wp-cli/core-command/src/WP_CLI/Core/CoreUpgrader.php on line 30
Warning: Declaration of WP_CLI\Core\CoreUpgrader::download_package($package, $check_signatures = true) should be compatible with WP_Upgrader::download_package($package, $check_signatures = false, $hook_extra = Array) in phar:///usr/local/bin/wp/vendor/wp-cli/core-command/src/WP_CLI/Core/CoreUpgrader.php on line 30
Warning: Failed to create directory '/.wp-cli/cache/': mkdir(): Permission denied.
https://downloads.wordpress.org/release/ja/wordpress-5.5.3.zip から更新をダウンロード中...
更新を展開しています…

[05-Nov-2020 02:08:26 UTC] PHP Warning:  chmod(): Operation not permitted in /home/kusanagi/kusajp/DocumentRoot/wp-admin/includes/class-wp-filesystem-direct.php on line 173
Warning: chmod(): Operation not permitted in /home/kusanagi/kusajp/DocumentRoot/wp-admin/includes/class-wp-filesystem-direct.php on line 173
<p>更新が必要な翻訳が一部あります。更新が終わるまでしばらくお持ちください。</p>
<div class="update-messages lp-show-latest"><h2> (ja) の翻訳を更新しています&#8230;</h2><p><span class="code">https://downloads.wordpress.org/translation/plugin/hello-dolly/1.7.2/ja.zip</span> から翻訳をダウンロード中&#8230;</p>
<p>更新を展開しています…</p>
<p>最新のバージョンをインストールしています…</p>
<p>古いバージョンの翻訳を削除中&#8230;</p>
<p>翻訳が正常に更新されました。</p>
</div><script type="text/javascript">
                                        (function( wp ) {
                                                if ( wp && wp.updates && wp.updates.decrementCount ) {
                                                        wp.updates.decrementCount( "translation" );
                                                }
                                        })( window.wp );
                                </script>Success: WordPress updated successfully.

```

PHP Warning:がでましたが、更新は成功するようです。/.wp-cli/cacheを前もって作成するときえるのでしょうか？

```shell
$ wp core update
Success: WordPress is up to date.

$  wp core version
5.5.3

コンテナから出ます
$ exit
```

またWPプラグインの更新は、ダッシュボードから可能でした。
設定後に、⑨ パーミッションの設定の項目を再度行うことをおすすめします。
```Shell
$ cd $HOME/mydir/kusajp
$ docker-compose up -d
$ docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/"
$ docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
$ docker-compose exec ftp sh -c "chown kusanagi.kusanagi /home/kusanagi/kusajp/DocumentRoot/wp-content/"
```


---


# 例文　パミッション変更方法。
プラグインやテーマのインストール、データのアップロードなどの作業できないときに使用。
各dirとsubdirに対して指定や、dirを除いて、fileにだけ対して指定などの例文です。

```Shell
 $ docker-compose exec ftp sh -c "chmod 775 /home/kusanagi/kusajp/DocumentRoot/wp-content/uploads/"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/uploads/ -type d -exec chmod 775 {} +"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/uploads/ -type f -exec chmod 644 {} +"
 作業後
 $ docker-compose exec ftp sh -c "chmod -R 755 /home/kusanagi/kusajp/DocumentRoot/"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/ -type f -exec chmod 644 {} +"

 $ docker-compose exec ftp sh -c "chmod 707 /home/kusanagi/kusajp/DocumentRoot/wp-content/themes/"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/themes/ -type d -exec chmod 777 {} +"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/themes/ -type f -exec chmod 644 {} +"
 作業後
 $ docker-compose exec ftp sh -c "chmod -R 755 /home/kusanagi/kusajp/DocumentRoot/"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/ -type f -exec chmod 644 {} +"

 $ docker-compose exec ftp sh -c "chmod 707 /home/kusanagi/kusajp/DocumentRoot/wp-content/plugins/"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/plugins/ -type d -exec chmod 777 {} +"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/plugins/ -type f -exec chmod 644 {} +"
 作業後
 $ docker-compose exec ftp sh -c "chmod -R 755 /home/kusanagi/kusajp/DocumentRoot/"
 $ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/ -type f -exec chmod 644 {} +"
```


パーミッションの設定
（参考）https://qiita.com/chiruparu/items/0108442b57946081b0bf
自分の環境では、動作しませんでした。

```Shell
まずは、コンテナ起動。
$ cd $HOME/mydir/kusajp
$ docker-compose up -d

DocumentRootを750から755に変更
$ docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/"

DocumentRoot/wp-content配下のuploads以外は755のまま
$ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/ -type d -exec chmod 755{} +"

DocumentRoot/wp-content配下のuploadsを755から775に変更（uploads以下を775に）
$ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/uploads/ -type d -exec chmod 775{} +"

DocumentRoot/wp-content配下のlanguagesは750のまま（languages以下の750に）
$ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/wp-content/languages/ -type d -exec chmod 750{} +"
```


設定後、DocumentRoot以下のファイル：644、ディレクトリ：755とすれば、大きなトラブルは避けられるのでしょうか。

```Shell
$ docker-compose exec ftp sh -c "chmod -R 755 /home/kusanagi/kusajp/DocumentRoot/"
$ docker-compose exec ftp sh -c "find /home/kusanagi/kusajp/DocumentRoot/ -type f -exec chmod 644 {} +"
```

# 設定の時だけ、wp-content以下のdirに対して禁じ手の777を実行。
プラグインインストール、テーマインストールなど、すべて動作することは確認しました。
→　この対応で正しいと思えませんが、まずは、動作させるということなら。

```Shell
$ docker-compose exec ftp sh -c "chmod -R 777 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
設定後、適切なセキュリティーの設定へ。

```


# kusanagi-docker config pushのあと、WordPress動作しないとき、wp-content以下のdirに対して禁じ手の777を実行。ダッシュボードのエラー表示消去まで。
(pushが頻回なら、コマンド化していいのかも。)


```Shell
cd $HOME/mydir/kusajp 
kusanagi-docker config push
docker-compose exec ftp sh -c "chmod 440 /home/kusanagi/kusajp/wp-config.php"
docker-compose exec ftp sh -c "chmod -R 777 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/"
docker-compose exec ftp sh -c "chmod 755 /home/kusanagi/kusajp/DocumentRoot/wp-content/"
docker-compose exec ftp sh -c "chown kusanagi.kusanagi /home/kusanagi/kusajp/DocumentRoot/wp-content/"
```

# この記事で、できあがるdocker-compose.yml。
kusanagiが、コマンドで、ローカルにプロビジョンされていて、
phpのdir、iniファイルが出来ていると動作すると思います。

```YAML

version: '3'

networks:
  default:
    driver: bridge
    driver_opts:
      com.docker.network.driver.mtu: 9000

services:
  httpd:
    container_name: kusajp_httpd
    image: primestrategy/kusanagi-nginx:1.17.9-r0
    restart: always
    env_file:
      - .kusanagi
      - .kusanagi.httpd
    volumes:
      - kusanagi:/home/kusanagi
      - kusanagi:/etc/letsencrypt
      - kusanagi:/var/www/html/.well-known
      - ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分
    ports:
      - "80:8080"
      - "443:8443"
    extra_hosts:
      - "wp.localhost:127.0.0.1"

  certbot:
    container_name: kusajp_certbot
    image: certbot/certbot:v1.8.0
    restart: always
    network_mode: "service:httpd"
    env_file:
      - .kusanagi.httpd
    volumes:
      - kusanagi:/var/www/html/.well-known
      - kusanagi:/etc/letsencrypt
      - ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分
    command:
      - --version

## CONFIG
  config:
    container_name: kusajp_config
    restart: always
    build:
        context: ./wpcli
    user: "1000:1001"
    env_file:
      - .kusanagi
      - .kusanagi.wp
      - .kusanagi.db
    volumes:
      - database:/var/run/mysqld
      - kusanagi:/home/kusanagi
      - ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分
    command: wp --version

  php:
    container_name: kusajp_php
    image: primestrategy/kusanagi-php:7.4.9-r0
    restart: always
    env_file:
      - .kusanagi
      - .kusanagi.php
      - .kusanagi.mail
    network_mode: "service:httpd"
    volumes:
      - database:/var/run/mysqld
      - kusanagi:/home/kusanagi
      - ./php/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini  #※追加
      - ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分

## MYSQL 
  db:
    container_name: kusajp_db
    image: mariadb:10.5.5-focal
    restart: always
    user: "999:999"
    env_file:
      - .kusanagi.mysql
    network_mode: "service:httpd"
    volumes:
      - database:/var/run/mysqld
      - database:/var/lib/mysql
 
## FTPD
  ftp:
    container_name: kusajp_ftp
    image: primestrategy/kusanagi-ftpd:1.0.2-r1
    restart: always
    network_mode: "service:httpd"
    env_file:
      - .kusanagi
      - .kusanagi.wp
    volumes:
      - kusanagi:/home/kusanagi
      - ./contents/DocumentRoot/wp-content/themes/:/home/kusanagi/kusajp/DocumentRoot/wp-content/themes/  #※今回の追加部分


volumes:
  kusanagi:
  database:
```

以上。
