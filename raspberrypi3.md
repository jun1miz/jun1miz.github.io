# Raspberry Pi 3 に DHCP サーバを構築する

## Raspberry Pi OS Lite のセットアップ

1. イメージを SDカードに書き込む  

1. /boot ボリュームに下記ファイルを新規作成する
    * `ssh.txt` (空ファイル) ⇒ ssh を有効する
    * `wpa_supplicant.conf` ⇒ wifi を有効にする

        ```sh
        ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev  
        update_config=1  
        network={  
          ssid="HUMAX-ABCDE"  
          psk="************"  
        }
        ```

1. 電源を付ける

1. ssh 経由でログインする (IPアドレスを探す必要がある)

    ```sh
    # 初期ユーザID: pi 
    # 初期パスワード: raspberry

    $ ssh pi@192.168.0.103
    password: raspberry

    pi@raspberry:~ $
    ```

1. 初期パスワードを変更する

    ```sh
    $ passwd
    ```

1. 有線LANを固定IPに変更する  
    `/etc/dhcpcd.conf` を編集する

    ```sh
    # インタフェース名
    interface eth0
    # 固定IPアドレス
    static ip_address=192.168.0.10/24
    # デフォルトゲートウェイ
    static routers=192.168.0.1
    # 参照DNSサーバ
    static domain_name_servers=192.168.0.1 8.8.8.8
    ```

1. 無線LANを無効にする

    ```sh
    sudo iwconfig wlan0 txpower off
    # 再起動で適用される
    sudo shutdown -r now
    ```

## DHCP サーバのセットアップ

### DHCP のリース手順

1. DHCP クライアントは、`DHCPDISCOVER` メッセージをブロードキャストする。

1. DHCP サーバは DHCPDISCOVER メッセージに応答して、  
   IPアドレス他の情報を含む `DHCPOFFER` メッセージをダイレクト送信orブロードキャストする。

1. 複数 DHCP サーバが存在する場合、DHCP クライアントは複数の DHCPOFFER メッセージを受信することになるので、  
   一つの DHCP サーバを選択して `DHCPREQUEST` メッセージをブロードキャストする。

1. DHCPREQUEST メッセージを受信した DHCP サーバは、コンフィグレーション情報を含む `DHCPACK` メッセージを送信します。  
   DHCPACK メッセージの IPアドレスフィールドには、割り当てられたネットワークが挿入される。  
   要求されたIPアドレスが割り当てられないなど、DHCPREQUEST メッセージの要求に答えられない場合、`DHCPNAK` メッセージを送信する。

1. DHCPクライアントは DHCPACK メッセージを受信するとパラメータのチェックを行い、リース期間等を記録しておく。DHCKACK メッセージのより受信したコンフィグレーション情報に問題があった場合、DHCPクライアントは `DHCPDECLINE` メッセージを送信する。

1. DHCPクライアントは、`DHCPRELEASE` メッセージを送信することにより、ネットワークアドレスを開放する

    |メッセージ|クライアント|サーバ|説明
    |-|:-:|:-:|-
    |DHCP**DISCOVER**|o|-|DHCPサーバを見つける為のブロードキャスト
    |DHCP**OFFER**|-|o|DHCPDISCOVERへの応答としてコンフィグレーション情報を含む、DHCPサーバかDHCPクライアントに送信されるメッセージ
    |DHCP**REQUEST**|o|-|コンフィグレーション情報の割当要求＆選択されなかったDHCPサーバに知らせるためにブロードキャスト
    |DHCP**ACK**|-|o|クライアントに送られるネットワークアドレスを含むコンフィグレーション情報
    |DHCP**NAK**|-|o|クライアントに送られる要求の拒否
    |DHCP**DECLINE**|o|-|クライアントから送られる無効なコンフィグレーション情報を含むメッセージ
    |DHCP**RELEASE**|o|-|クライアントから送られるネットワークアドレス解放とリースのキャンセルメッセージ

### isc-dhcp-server を使う

1. インストールする

    ```sh
    sudo apt-get -y install isc-dhcp-server
    # ⇒インストール後に自動起動しようとするが設定が初期値のままなので失敗する
    ```

2. DHCPの構成を設定する  
    `/etc/dhcp/dhcpd.conf` を編集する
    
    ```sh
    # コメントアウト
    #option domain-name "example.org";
    #option domain-name-servers ns1.example.org, ns2.example.org;

    # 初期リース時間(秒) 60分 * 60秒 ⇒ 3600秒
    default-lease-time 3600;
    # 最大リース時間(秒) 8時間 * 60分 * 60秒 ⇒ 28800秒
    max-lease-time 28800;

    # ローカルネットワークの公式DHCPサーバとする(コメントアウト)
    authoritative;

    # 配布アドレスの設定
    subnet 192.168.0.0 netmask 255.255.255.0 {
        # 配布するホストアドレスの配布
        range 192.168.0.100 192.168.0.150;
        # デフォルトゲートウェイのアドレス
        option routers 192.168.0.1;
        # DNSサーバのアドレス
        option default-name-servers 8.8.8.8 4.4.4.4;
        # DHCPDECLINEメッセージを無視する
        ignore declines;
    }
    ```

    `/etc/default/isc-dhcp-server` を編集する

    ```sh
    INTERFACESv4="eth0"
    ```

3. DHCPサーバを起動

    ```sh
    # サーバ起動
    sudo systemctl start isc-dhcp-server.service
    # 状態の確認
    systemctl status isc-dhcp-server.service
    # 払い出し確認
    tail /var/lib/dhcp/dhcpd.leases
    # ログ
    tail -100 /var/log/syslog | grep -i "dhcp"
    ```

