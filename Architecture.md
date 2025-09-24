```mermaid
graph TD
    subgraph "外部 (ユーザー側)"
        A[業務委託者 (30人)]
    end

    subgraph "Azure クラウド"
        B(AVDサービス/管理プレーン)
        C(Azure AD / Entra ID)
        
        subgraph "VNet (仮想ネットワーク)"
            direction LR
            D[AVDホストプール <br> (セッションホストVM 4~5台)]
            E[社内サーバー (VM 2台)]
        end

        F[NSG (ネットワークセキュリティグループ)]
    end

    A -- インターネット --> B
    B -- 認証 --> C
    B -->|接続| D
    
    D -- プライベート接続 (RDP) --> E
    D -- 認証 --> C
    E -- 認証 --> C

    Azureクラウド -->|保護| F
    F -- 許可 --> D
    F -- 許可 --> E
```
