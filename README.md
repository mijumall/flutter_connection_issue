# Flutterコンテナにアクセスできない問題(解決)

```shell
git clone https://github.com/mijumall/flutter_connection_issue.git
cd flutter_connection_issue
docker-compose up -d
docker-compose ps # きちんと動いてるか確認
```

# 困ってること
Nginx経由でFlutterのカウンターアプリに飛びたいが、なぜかできない。


Portとサービス
* 4001... Nginxだけど、ルートは404。詳細はnginx.confを参照
* 4002... Flutter デフォルトのカウンターアプリ
* 4003... FastAPIがこんにちは。

ブラウザを開き、以下の場所にアクセス

http://localhost:4001/fastapi

4003にプロキシで飛ばされる。想定通り。

同様に、nginx経由でFlutterのカウンターアプリに飛んでみようとするが、うまくいかない。一応返答は返ってくるようだが、何もない画面しかうつらない。
http://localhost:4001/flutter 

# 追記(2月10日): 解決方法
前提として、Flutterは以下の場所で作動（デフォルトのカウンターアプリが開く）。
http://localhost:4002/

このアプリをたとえば `/home`をルートとして走らせたい場合、つまり
http://localhost:4002/home/
からアクセスできるようにしたい場合、Flutterプロジェクトフォルダ内の`web/index.html`を開き、  
```html
<base href="$FLUTTER_BASE_HREF">
```
を
```html
<base href="/home/">
```
に変更することで達成可能。ちなみに$FLUTTER_BASE_HEREはデフォルトで`/`に置き換わるよう設定されている。

___ 

次に、Nginxをプロキシサーバーとして置き、その後ろでFlutterアプリを動かす場合を考える。Nginxはポート4001で動かすとする。

やりたいこと:
Nginxサーバーが走る http://localhost:4001/home にアクセスすると、http://localhost:4002/home で作動しているFlutterのカウンターアプリが開く。

手順1: 
Flutterプロジェクト内の `web/index.html` を開き、base href で/home/を指定。
```html
<base href="/home/">
```

手順2:
nginx.conf でプロキシパスを設定。__このとき、locationをFlutterアプリのルートURLと同じ`/home`に指定する。__
```
	listen 4001; 
	location /home {
		proxy_pass http://flutter:4002/home/;
	}
```

以上の手順を踏むと、localhost:4001/home からでも localhost:4002/home のアプリを開けるようになる。

アプリをビルドする際は、`flutter web build --base-href /home/`でhrefを任意の場所に指定できる。

__マリーさん、アドバイスありがとうございます!__
