---
title: 使用 Defender for IoT CLI 命令
description: 本文說明適用于感應器和內部部署管理主控台的 Defender for IoT CLI 命令。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 48c525004b095c1e0f498f86a5395d0002be26f1
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845253"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>使用 Defender for IoT CLI 命令

本文說明適用于感應器和內部部署管理主控台的 CLI 命令。 系統管理員可以存取命令、cyberx 使用者，以及支援使用者。

當您正在規劃維護活動或不需要警示的活動時，請定義排除規則。

## <a name="create-local-alert-exclusion-rules"></a>建立本機警示排除規則

您可以在 CLI 中輸入下列命令來建立排除規則：

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

您可以在警示排除規則內定義的屬性如下所示：

| 屬性 | 描述 |
|--|--|
| [-h] | 列印命令的說明資訊。 |
| -n 名稱 | 所建立之規則的名稱。 |
| [-ts 時間] | 規則作用中的時間範圍。 這應該指定為：<br />`xx:yy-xx:yy`<br />您可以使用逗號之間的逗號來定義一個以上的時間週期。 例如： `xx:yy-xx:yy, xx:yy-xx:yy` 。 |
| [-dir 方向] | 套用規則的方向。 這應該指定為：<br />`both | src | dst` |
| [-開發裝置] | 規則要排除之裝置的 IP 位址和網址類別型，指定為：<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-警示] | 規則將排除的警示名稱：<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>附加本機警示排除規則

您可以在 CLI 中輸入下列命令，將新規則新增至目前的警示排除規則：

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

此處使用的屬性類似于建立本機警示排除規則時所描述的屬性。 在這裡的用法中，屬性會套用至現有的規則。

## <a name="show-local-alert-exclusion-rules"></a>顯示本機警示排除規則

輸入下列命令以查看所有現有的排除規則：

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>刪除本機警示排除規則

您可以輸入下列命令來刪除現有的警示排除規則：

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

您可以搭配使用下列屬性與警示排除規則：

| 屬性 | 描述|
| --------- | ---------------------------------- |
| -n 名稱 | 要刪除的規則名稱。 |

## <a name="sync-time-from-the-ntp-server"></a>從 NTP 伺服器同步處理時間

您可以從 NTP 伺服器啟用和停用時間同步。

### <a name="enable-ntp-sync"></a>啟用 NTP 同步

輸入下列命令可讓您從指定的 NTP 伺服器定期抓取目前時間：

```azurecli-interactive
ntp enable IP
```

您可以在命令內定義的屬性是 NTP 伺服器的 IP 位址。

### <a name="disable-ntp-sync"></a>停用 NTP 同步

輸入下列命令將會停用與指定 NTP 伺服器的時間同步：

```azurecli-interactive
ntp disable IP
```

您可以在命令內定義的屬性是 NTP 伺服器的 IP 位址。

## <a name="configure-the-network"></a>設定網路

下表說明可用於設定適用于 IoT 的 Azure Defender 網路選項的命令：

|名稱|Command|描述|
|-----------|-------|-----------|
|Ping|`ping IP `| Ping 針對 IoT 平臺的 Defender 以外的位址。|
|Blink|`network blink`|啟用變更網路設定參數。|
|重新設定網路 |`network edit-settings`| 啟用變更網路設定參數。 |
|顯示網路設定 |`network list`|顯示網路介面卡參數。 |
|驗證網路設定 |`network validate` |提供輸出網路設定。 <br /> <br />例如： <br /> <br />目前的網路設定： <br /> 介面： eth0 <br /> ip：10.100.100。1 <br />子網：255.255.255。0 <br />預設閘道：10.100.100.254 <br />dns：10.100.100.254 <br />監視介面： eth1|
|匯入憑證 |`certificate import FILE` |匯入 HTTPS 憑證。 您必須指定完整路徑，這會導致 \* .crt 檔。 |
|顯示日期 |`date` |以 GMT 格式傳回主機上的目前日期。 |

## <a name="filter-network-configurations"></a>篩選網路設定

此 `network capture-filter` 命令可讓系統管理員消除不需要分析的網路流量。 使用包含清單或排除清單來篩選流量。

```azurecli-interactive
network capture-filter
```

輸入命令之後，系統會提示您輸入下列問題：

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

選取 `Y` 即可開啟 nano 檔案，您可以在其中根據下列語法新增裝置、通道、埠和子集：

| 屬性 | 描述 |
|--|--|
| 1.1.1.1 | 包含此裝置的所有流量。 |
| 1.1.1.1、2.2.2。2 | 包含此通道的所有流量。 |
| 1.1.1，2.2。2 | 包含此子網的所有流量。 |

藉由卸載資料列來分隔引數。

當您包含裝置、通道或子網時，感應器會處理該引數的所有有效流量，包括通常不會處理的埠和流量。

然後，系統會要求您執行下列動作：

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

選取 `Y` 即可開啟 nano 檔案，您可以在其中根據下列語法新增裝置、通道、埠和子集：

| 屬性 | 描述 |
|--|--|
| 1.1.1.1 | 排除此裝置的所有流量。 |
| 1.1.1.1、2.2.2。2 | 排除此通道的所有流量，表示兩個裝置之間的所有流量。 |
| 1.1.1.1、2.2.2.2、443 | 依埠排除此通道的所有流量。 |
| 1.1.1 | 排除此子網的所有流量。 |
| 1.1.1，2.2。2 | 排除子網之間的所有流量。 |

藉由卸載資料列來分隔引數。

當您排除裝置、通道或子網時，感應器會排除該引數的所有有效流量。

### <a name="ports"></a>連接埠

包含或排除所有流量的 UDP 和 TCP 埠。

>`502`：單一端口

>`502,443`：兩個埠

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>元件

系統會要求您提供下列內容：

>`In which component do you wish to apply this capture filter?`

您的選項為：  `all` 、 `dissector` 、 `collector` 、 `statistics-collector` 、 `rpc-parser` 或 `smb-parser` 。

在大部分的使用案例中，選取 `all` 。

### <a name="custom-base-capture-filter"></a>自訂基底捕獲篩選

基底捕獲篩選器是元件的基準。 例如，篩選準則會決定可供元件使用的埠。

`Y`針對下列所有選項選取。 設定變更之後，所有篩選都會新增至基準。 如果您進行變更，將會覆寫現有的基準。

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

如果您選擇排除子網，例如1.1.1：

- `internal` 只會排除該子網。

- `all-connected` 將會排除該子網，以及進出該子網的所有流量。

建議您選取 [] `internal` 。

> [!NOTE]
> 您的選擇會用於工具中的所有篩選準則，且不會相依于會話。 換句話說，您不能選擇 `internal`   某些篩選器和其他篩選準則  `all-connected`   。

### <a name="comments"></a>註解

您可以在下列情況中查看篩選  ```/var/cyberx/properties/cybershark.properties``` ：

- **統計資料-收集器**：  `bpf_filter property` in ```/var/cyberx/properties/net.stats.collector.properties```

- **dissector**： `override.capture_filter`   中的屬性 ```/var/cyberx/properties/cybershark.properties```

- **rpc** 剖析器： `override.capture_filter` 中的屬性 ```/var/cyberx/properties/rpc-parser.properties```

- **smb** 剖析器：  `override.capture_filter`   中的屬性 ```/var/cyberx/properties/smb-parser.properties```

- **收集器**： `general.bpf_filter`   中的屬性 ```/var/cyberx/properties/collector.properties```

您可以針對 cyberx 使用者輸入下列程式碼，來還原預設設定：

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>定義用戶端和伺服器主機

如果 Defender for IoT 未自動偵測到用戶端和伺服器主機，請輸入下列命令來設定用戶端和伺服器主機：

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

您可以搭配命令使用下列屬性 `directions` ：

| 屬性 | 描述 |
|--|--|
| [-h] | 列印命令的說明資訊。 |
| [--識別碼識別碼] | 伺服器識別碼。 |
| [--埠埠] | 伺服器埠。 |
| [--移除] | 從清單中移除用戶端或伺服器主機。 |
| [--add] | 將用戶端或伺服器主機新增至清單。 |
| [--tcp] | 與此主機通訊時，請使用 TCP。 |
| [--udp] | 與此主機通訊時，請使用 UDP。 |

## <a name="system-actions"></a>系統動作
下表說明可在 Defender for IoT 內執行各種系統動作的命令：

|名稱|程式碼|描述|
|----|----|-----------|
|重新開機主機|`system reboot`|重新開機主機裝置。|
|關閉主機|`system shutdown`|將主機關機。|
|備份系統|`system backup`| (未排程的備份) 起始立即備份。|
|從備份還原系統|`system restore`|從最新的備份還原。|
|列出備份檔案|`system backup-list`|列出可用的備份檔案。|
|顯示 IoT 平臺服務的所有 Defender 狀態|`system sanity`|藉由列出 IoT 平臺服務的所有 Defender 目前狀態來檢查系統效能。|
|顯示軟體版本|`system version`|顯示目前正在系統上執行的軟體版本。|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>將 SSL 和 TLS 憑證部署到設備

輸入下列命令，將 SSL 和 TLS enterprise 憑證匯入至 CLI：

```azurecli-interactive
cyberx-xsense-certificate-import
```
若要使用此工具，您必須將憑證檔案上傳至裝置。 您可以透過 WinSCP 或 Wget 等工具來執行此動作。 

此命令支援下列輸入旗標：

| 旗標 | 描述 |
|--|--|
| -H | 顯示命令列説明語法。 |
| --crt | 憑證檔案的路徑 ( .crt 副檔名) 。 |
| --key | \*金鑰檔。 金鑰長度至少應為2048位。 |
| --鏈 | 憑證鏈檔案的路徑 (選擇性) 。 |
| --pass | 用來加密憑證 (選用) 的複雜密碼。 |
| --複雜密碼設定 | 預設值為 **False**， **未使用**。 <br />設定為 **True** 以使用先前憑證提供的先前複雜密碼 (選擇性) 。 |  |

當您使用此工具時：

- 確認憑證檔案在設備上是可讀取的。 

- 使用您的 DNS 伺服器和對應的 IP 位址在憑證) 中顯示的憑證，確認設備網域 (。 
    
## <a name="see-also"></a>請參閱

[適用于 IoT 的 Defender API 感應器和管理主控台 Api](references-work-with-defender-for-iot-apis.md)
