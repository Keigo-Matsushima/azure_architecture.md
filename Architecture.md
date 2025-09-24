```mermaid

graph TD
    subgraph "外部 (ユーザー側)"
        A[ユーザーPC (30人)]
    end

    subgraph "Azure クラウド"
        subgraph VNET
            subgraph "AVD 環境"
                subgraph "AVD ホストプール"
                    VM_AVD1(セッションホストVM 1)
                    VM_AVD2(セッションホストVM 2)
                    VM_AVD3(セッションホストVM 3)
                    VM_AVD4(セッションホストVM 4)
                end
            end
            subgraph "社内サーバー (顧客情報)"
                VM_SRV1(VM 1)
                VM_SRV2(VM 2)
            end
        end

        AZ_AD[Azure AD]
        AVD_GW[AVDゲートウェイ/管理プレーン]
        NSG[NSG (ネットワークセキュリティグループ)]
    end

    A -- インターネット --> AVD_GW
    AVD_GW -- 認証 --> AZ_AD
    AVD_GW -->|接続リダイレクト| VM_AVD1
    AVD_GW -->|接続リダイレクト| VM_AVD2
    AVD_GW -->|接続リダイレクト| VM_AVD3
    AVD_GW -->|接続リダイレクト| VM_AVD4
    
    VM_AVD1 -- RDP --> VM_SRV1
    VM_AVD2 -- RDP --> VM_SRV1
    VM_AVD3 -- RDP --> VM_SRV2
    VM_AVD4 -- RDP --> VM_SRV2
    
    VNET -- 受信/送信 --> NSG
    NSG -- 許可 --> VM_AVD1
    NSG -- 許可 --> VM_AVD2
    NSG -- 許可 --> VM_AVD3
    NSG -- 許可 --> VM_AVD4
    NSG -- 許可 --> VM_SRV1
    NSG -- 許可 --> VM_SRV2
    
    AZ_AD -- 認証 --> VM_SRV1
    AZ_AD -- 認証 --> VM_SRV2
```
