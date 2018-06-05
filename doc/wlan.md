wlan
===
WiFiの設定方法です。

使用デバイス
---
- Intel Wireless LAN Adapter (4965)

インターフェースの設定
---
- `/etc/rc.conf` へ以下の記述を追加します。

```
wlans_iwn0="wlan0"
ifconfig_wlan0="country JP WPA DHCP"
```

WiFiアダプターへ固定IPを設定する場合は、以下のように記述します。

- IPアドレスとネットマスクは、ネットワーク環境に合わせて変更します。

```
ifconfig_wlan0="inet 192.168.0.10 netmask 255.255.255.0 JP WPA"
```

アクセスポイントの登録
---
- 接続先のアクセスポイントは、`/etc/wpa_supplicant.conf` へ以下の様に記述します。

```
network={
    ssid = "ssid"                  # アクセスポイントのSSIDを指定
    scan_ssid = 1
    proto = WPA2
    key_mgmt = WPA-PSK
    psk = "password"               # SSIDのパスワードを指定
}
```