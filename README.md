# private-isu-try

## 環境

さくらのクラウド 東京第二ゾーン 2core/1GBサーバ

## 初期スコア

```
{"pass":true,"score":1857,"success":1994,"fail":11,"messages":["リクエストがタイムアウトしました (GET /)","リクエストがタイムアウトしました (GET /logout)","リクエストがタイムアウトしました (GET /posts)","リクエストがタイムアウトしました (POST /login)"]}
{"pass":true,"score":1914,"success":1992,"fail":8,"messages":["リクエストがタイムアウトしました (GET /)","リクエストがタイムアウトしました (GET /logout)","リク エストがタイムアウトしました (GET /posts)","リクエストがタイムアウトしました (POST /login)"]}
{"pass":true,"score":2053,"success":2113,"fail":7,"messages":["リクエストがタイムアウトしました (GET /)","リクエストがタイムアウトしました (GET /logout)","リク エストがタイムアウトしました (GET /posts)","リクエストがタイムアウトしました (POST /login)"]}
```

## post_id_idx 追加

```
mysql> ALTER TABLE `comments` ADD INDEX `post_id_idx` (`post_id`);
Query OK, 0 rows affected (0.63 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

score

```
{"pass":true,"score":11531,"success":9791,"fail":0,"messages":[]}
{"pass":true,"score":11715,"success":9938,"fail":0,"messages":[]}
{"pass":true,"score":11207,"success":9529,"fail":0,"messages":[]}
```

## my.conf

追加

```
innodb_flush_method=O_DIRECT
innodb_flush_log_at_trx_commit = 2
disable-log-bin = 1
```

score

```
{"pass":true,"score":11087,"success":9361,"fail":0,"messages":[]}
{"pass":true,"score":11533,"success":9674,"fail":0,"messages":[]}
{"pass":true,"score":11221,"success":9507,"fail":0,"messages":[]}
```

## post_id_idx 張り替え

```
mysql> ALTER TABLE `comments` DROP INDEX `post_id_idx`, ADD INDEX `post_id_idx` (`post_id`, `created_at` DESC);
Query OK, 0 rows affected (0.42 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

score

```
{"pass":true,"score":11379,"success":9617,"fail":0,"messages":[]}
{"pass":true,"score":11106,"success":9315,"fail":0,"messages":[]}
{"pass":true,"score":11135,"success":9352,"fail":0,"messages":[]}
```

## order by狙いのkey

```
mysql> alter table posts add index posts_order_idx (created_at DESC);
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

score


```
{"pass":true,"score":11338,"success":9562,"fail":0,"messages":[]}
{"pass":true,"score":11295,"success":9499,"fail":0,"messages":[]}
{"pass":true,"score":11330,"success":9481,"fail":0,"messages":[]}
```

## user_idx追加

```
mysql> ALTER TABLE `comments` ADD INDEX `idx_user_id` (`user_id`);
Query OK, 0 rows affected (0.34 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

score

```
{"pass":true,"score":12208,"success":10351,"fail":0,"messages":[]}
{"pass":true,"score":11716,"success":9811,"fail":0,"messages":[]}
{"pass":true,"score":11597,"success":9701,"fail":0,"messages":[]}
```

## JOIN

score

```
{"pass":true,"score":10287,"success":8772,"fail":4,"messages":["response code should be 200, got 502 (GET /)","response code should be 200, got 502 (POST /login)"]}
{"pass":true,"score":10491,"success":8938,"fail":0,"messages":[]}
{"pass":true,"score":10371,"success":8774,"fail":0,"messages":[]}
```

## postsPerPage


score

```
{"pass":true,"score":18957,"success":14918,"fail":0,"messages":[]}
{"pass":true,"score":19263,"success":15201,"fail":0,"messages":[]}
{"pass":true,"score":19088,"success":14943,"fail":0,"messages":[]}
```

## reduce N+1 in makePosts

```
{"pass":true,"score":20707,"success":16220,"fail":0,"messages":[]}
{"pass":true,"score":20570,"success":16099,"fail":0,"messages":[]}
{"pass":true,"score":21075,"success":16575,"fail":0,"messages":[]}
```

## post_user_idx

```
mysql> ALTER TABLE posts ADD INDEX posts_user_idx (user_id, created_at DESC);
Query OK, 0 rows affected (0.20 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

score

```
{"pass":true,"score":11824,"success":10273,"fail":0,"messages":[]}
{"pass":true,"score":11923,"success":10318,"fail":0,"messages":[]}
{"pass":true,"score":12334,"success":10726,"fail":0,"messages":[]}
```

## Straight join

score

```
{"pass":true,"score":23417,"success":18320,"fail":0,"messages":[]}
{"pass":true,"score":22620,"success":17615,"fail":0,"messages":[]}
{"pass":true,"score":22510,"success":17545,"fail":0,"messages":[]}
```

## tune mysql driver

score

```
{"pass":true,"score":26054,"success":20131,"fail":0,"messages":[]}
{"pass":true,"score":24823,"success":19254,"fail":0,"messages":[]}
{"pass":true,"score":24870,"success":19306,"fail":0,"messages":[]}
```

## nginx静的ファイル

```
  location /css/ {
    root /home/isucon/private_isu/webapp/public/;
    expires 1d;
  }

  location /js/ {
    root /home/isucon/private_isu/webapp/public/;
    expires 1d;
  }
```

score

```
{"pass":true,"score":24783,"success":19185,"fail":0,"messages":[]}
{"pass":true,"score":25601,"success":19929,"fail":0,"messages":[]}
{"pass":true,"score":24114,"success":18659,"fail":0,"messages":[]}
```

## nginx keepalive

```
upstream app {
  server localhost:8080;
  keepalive 100;
  keepalive_requests 10000;
}

server {
  location / {
    proxy_set_header Host $host;
    proxy_http_version 1.1;
    proxy_set_header Connection "";
    proxy_pass http://app;
    #proxy_pass http://localhost:8080;
  }
}
```

score

```
{"pass":true,"score":25181,"success":19458,"fail":0,"messages":[]}
{"pass":true,"score":26567,"success":20568,"fail":0,"messages":[]}
{"pass":true,"score":25108,"success":19349,"fail":0,"messages":[]}
```


## 外部コマンド

score

```
{"pass":true,"score":25820,"success":19993,"fail":0,"messages":[]}
{"pass":true,"score":26260,"success":20327,"fail":0,"messages":[]}
{"pass":true,"score":26218,"success":20275,"fail":0,"messages":[]}
```

## imageをnginxで配信

nginx

```
  location /image/ {
    root /home/isucon/private_isu/webapp/public/;
    try_files $uri $uri/ @app;
    expires 1d;
  }

  location @app {
    proxy_set_header Host $host;
    proxy_http_version 1.1;
    proxy_set_header Connection "";
    proxy_pass http://app;
  }
```

score

```
{"pass":true,"score":102228,"success":96402,"fail":0,"messages":[]}
{"pass":true,"score":99284,"success":93499,"fail":0,"messages":[]}
{"pass":true,"score":103508,"success":97773,"fail":0,"messages":[]}
```

## no store imgdata to DB

```
mysql> CREATE TABLE `images` (
    ->   `id` int NOT NULL,
    ->   `mime` varchar(64) NOT NULL,
    ->   `imgdata` mediumblob NOT NULL,
    ->   PRIMARY KEY (`id`)
    -> ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO `images` (`id`,`mime`,`imgdata`) SELECT `id`,`mime`,`imgdata` FROM `posts`;
Query OK, 10157 rows affected (1 min 36.56 sec)
Records: 10157  Duplicates: 0  Warnings: 0

mysql> UPDATE `posts` SET `imgdata` = '';
Query OK, 10157 rows affected (24.60 sec)
Rows matched: 10157  Changed: 10157  Warnings: 0

mysql> ALTER TABLE `posts` Engine=InnoDB;
Query OK, 0 rows affected (2.40 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

score

```
{"pass":true,"score":85899,"success":82084,"fail":0,"messages":[]}
{"pass":true,"score":89106,"success":85166,"fail":0,"messages":[]}
{"pass":true,"score":94567,"success":90438,"fail":0,"messages":[]}
```

