# Decision Log

### 決定：EC2をPrivate Subnetに配置する

- **状況**：Public Subnetに置けば構成は簡単だが、EC2がインターネットから直接アクセス可能になる。
- **選んだもの**：EC2をPrivate Subnetに配置し、外部通信はALB経由とした。
- **捨てたもの**：Public Subnet配置による構成の単純化と、NAT Gateway不要によるコスト削減。
- **不都合**：外向き通信にはNAT Gatewayが必要となり、構成とコストが増える。

### 決定：データベースはAmazon RDSを採用する

- **状況**：DBをEC2上に構築すると、OS・バックアップ・パッチ管理が必要になる。
- **選んだもの**：Amazon RDS for MySQL。
- **捨てたもの**：EC2上にMariaDB／MySQLを構築する構成。
- **不都合**：OSやDB環境の細かなカスタマイズに制約がある。

### 決定：RDSはSingle-AZで構築する

- **状況**：Multi-AZも検討したが、今回の利用環境では選択できなかった。
- **選んだもの**：Single-AZ構成。
- **捨てたもの**：Multi-AZ構成。
- **不都合**：AZ障害時の自動フェイルオーバーができない。
- **確かめ方**：RDSの詳細画面でSingle-AZを確認し、2台のEC2から接続できることを確認した。

### 決定：NAT Gatewayは1台のみ使用する

- **状況**：Private EC2の外向き通信にはNAT Gatewayが必要だが、学習環境ではコストを抑えたかった。
- **選んだもの**：1台のNAT Gatewayを複数AZで共用。
- **捨てたもの**：各AZにNAT Gatewayを配置する構成。
- **不都合**：NAT配置AZの障害時に外向き通信へ影響が出る。
- **確かめ方**：各Private Subnetのルートが同じNAT Gatewayを向いていることを確認した。