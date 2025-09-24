```mermaid
graph LR
    subgraph "外部 (ユーザー側)"
        A[ユーザーPC (30人)]
    end

    subgraph "Azure クラウド"
        subgraph VNET
            subgraph "AVD (Azure Virtual Desktop) 環境"
                subgraph "AVD 管理プレーン"
                    AVD_MP(AVD管理プレーン)
                end
                subgraph "AVD ホストプール (セッションホストVM)"
                    direction LR
                    AVD_VM1(VM 1) --- AVD_VM2(VM 2) --- AVD_VM3(VM 3) --- AVD_VM4(VM 4) --- AVD_VM5(VM 5)
                end
                AVD_MP --- AVD_VM1
            end

            subgraph "社内サーバー (顧客情報)"
                direction LR
                SRV_VM1(VM A) --- SRV_VM2(VM B)
            end

            AVD_VM1 -->|プライベートIP (RDP)| SRV_VM1
            AVD_VM2 -->|プライベートIP (RDP)| SRV_VM1
            AVD_VM3 -->|プライベートIP (RDP)| SRV_VM1
            AVD_VM4 -->|プライベートIP (RDP)| SRV_VM1
            AVD_VM5 -->|プライベートIP (RDP)| SRV_VM1
        end

        subgraph "セキュリティ・ID管理"
            AZ_AD[Azure AD]
            NSG_VNET[NSG (VNetファイアウォール)]
            NSG_AVD[NSG (AVDホストプール)]
            NSG_SRV[NSG (社内サーバー)]
        end

        AVD_MP -- 認証 --> AZ_AD
        AVD_VM1 -- 認証 --> AZ_AD
        SRV_VM1 -- 認証 --> AZ_AD
    end

    A -- インターネット --> AVD_MP
    AVD_MP -- アクセス制御 --> NSG_VNET
    NSG_VNET -- アクセス許可 --> NSG_AVD
    NSG_AVD -- アクセス許可 --> AVD_VM1
    AVD_MP -. 管理/制御 .-> AVD_VM1
    AVD_VM1 -- アクセス許可 --> NSG_SRV
    NSG_SRV -- アクセス許可 --> SRV_VM1
