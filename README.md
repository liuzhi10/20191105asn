## 提出課題: データ転送時間測定プログラムの作成

以下の機能を満たすサーバ bwcheck_server.pyとクライアントbwcheck_client.pyを作成し、pbl1とpbl2間、pbl1とpbl3間でのデータ通信速度（スループット）を計測してください。

### 全般
  - サーバはマルチスレッドで動作し、最大10台の接続待ちクライアントを許容する。
  - サーバが使用するポート番号は、50000 + 自分の学籍番号の下2桁 * 100 + 0〜99の任意の数値とする。
  - データの転送はTCPを用いるものとする（UDPを使っても良いのですが、 エラーがある場合にも対応したまともなものを作ろうとすると格段に課題の難易度が上がりますので、時間内に完成できる人がほとんどいなくなってしまうと思います。）

### サーバ
  - サーバはコマンドライン引数を指定しないで起動する。
  - サーバは、クライアントから"数値\n"という形式の要求メッセージ（例: '5242288\n'）を受信すると、応答コードを返した後、指定されたサイズのデータを送信する。送信するデータはランダムなバイト列とする。各バイトはrandom.randrange(256)で生成できる。要 import random。
    - 応答コード
      - 100 OK
      - 200 Invalid data size (Negative or Too large)
      - 300 Invalid request
  - データを送りきったら、ソケットをクローズする。

### クライアント

- クライアントは第1引数にサーバのホスト名あるいはIPアドレス、第2引数にサーバのポート番号、第3引数に転送するデータサイズ（バイト単位）を受け付けるものとする。第3引数で与えるデータサイズの典型的な値は524288（512Kバイト）、1048576（1Mバイト）など。
- クライアントは指定したサーバのポートに要求するデータサイズを10進数で表記したメッセージ"数値\n"（例えば"1048576\n"を送信する。
- クライアントは要求メッセージを送信後、サーバからの応答を改行が届くまで待つ。この処理は、1バイトずつ受信して、受け取ったバイトcが`c == b'\n`を満たすかどうかをチェックすることで実現できる。なお、`b'\n'`は、バイトデータとしての改行文字（`\n`）を表している。
   - サーバからの応答（改行文字の前まで）を受け取るには以下のようにするとよい。改行文字を受信するまで、1バイトずつ`b = socket.recv(1)[0]`で受信して、そのたびに前もって`recv_bytearray = bytearray()`で初期化しておいたbytearrayに、`recv_bytearray.append(b)`として加えていく。改行文字が検出されたときに、`recv_str = recv_bytearray.decode()`とすればよい。
- サーバから届く最初のメッセージの空白前の3文字の文字列で応答の内容を判別する。'100'であれば残りのデータの受信を開始する。この時、時刻を記録する。そうでなければ、エラーメッセージを出力して終了する。
- 改行よりあとに自分が指定したサイズが届けられるまで、データを受け取る。
- 指定したサイズのデータが届いたら、時刻を計測し、転送速度(=受信データサイズ/経過時間）を計算して、bit per second (bps)単位で画面に出力し、終了する。
- 要求メッセージが不正な形だった場合は、サーバから受信した応答コードを画面に表示してプログラムを終了する。


