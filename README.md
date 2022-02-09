# Flutterコンテナにアクセスできない問題（一部解決）

```shell
git clone https://github.com/mijumall/flutter_connection_issue.git
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