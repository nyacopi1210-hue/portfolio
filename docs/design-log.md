# Decision Log

## 1. EC2をPrivate Subnetに配置

### 状況
EC2をPublic Subnetに配置すると構成は単純になるが、EC2がインターネットから直接アクセス可能になる。

### 選んだもの
EC2をPrivate Subnetに配置し、外部からの通信はALB経由とした。

### 捨てたもの
EC2をPublic Subnetに配置する構成。

### 承知の上で受け入れた不都合
Private Subnet内のEC2からインターネットへ通信するために、NAT Gatewayが必要となり、構成とコストが増える。

### 確かめ方
- EC2にPublic IPv4アドレスが付与されていないことを確認した。
- ALBのDNS名へアクセスし、Webページが表示されることを確認した。

## 2. DBをEC2上ではなくRDSで構築

### 状況
EC2上にMySQLを構築する方法もあるが、OS管理、バックアップ、パッチ適用などの運用負荷が増える。

### 選んだもの
Amazon RDS for MySQLを利用した。

### 捨てたもの
EC2上にMySQLまたはMariaDBを構築する構成。

### 承知の上で受け入れた不都合
OSレベルの細かな設定や、データベース環境の自由度は低くなる。

### 確かめ方
2台のEC2からRDSへMySQL接続し、接続できることを確認した。

## 3. RDSはSingle-AZで構築

### 状況
可用性を高めるためMulti-AZ構成も検討したが、今回の学習環境ではコストを抑えることを優先した。

### 選んだもの
RDSをSingle-AZで構築した。

### 捨てたもの
RDSのMulti-AZ構成。

### 承知の上で受け入れた不都合
AZ障害時の自動フェイルオーバーがなく、データベースが単一障害点となる。

### 確かめ方
- RDSの設定画面でSingle-AZであることを確認した。
- 2台のEC2からRDSへ接続できることを確認した。

## 4. NAT Gatewayは1台構成

### 状況
Private Subnet内のEC2からパッケージ更新などの外向き通信を行うため、NAT Gatewayが必要だった。

### 選んだもの
学習環境ではNAT Gatewayを1台のみ配置し、複数AZのPrivate Subnetから共有する構成とした。

### 捨てたもの
各AZにNAT Gatewayを1台ずつ配置する構成。

### 承知の上で受け入れた不都合
NAT Gatewayを配置したAZに障害が発生した場合、他AZのEC2も外向き通信できなくなる可能性がある。

### 確かめ方
- 各Private Subnetのルートテーブルで `0.0.0.0/0` が同じNAT Gatewayを向いていることを確認した。
- 2台のEC2で `curl https://checkip.amazonaws.com` を実行し、インターネットへ通信できることを確認した。