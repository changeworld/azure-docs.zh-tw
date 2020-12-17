---
title: 為 Azure NetApp Files 設定 Azure 應用程式一致的快照集工具 |Microsoft Docs
description: 提供執行 Azure 應用程式一致快照集工具（可搭配 Azure NetApp Files 使用）的 [設定] 命令的指南。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632643"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>設定 Azure 應用程式一致的快照集工具 (預覽) 

本文提供的指南說明如何執行 Azure 應用程式一致的快照集工具（可與 Azure NetApp Files 搭配使用）的 [設定] 命令。

## <a name="introduction"></a>簡介

您可以使用命令來建立或編輯設定檔 `azacsnap -c configure` 。

## <a name="command-options"></a>命令選項

此 `-c configure` 命令有下列選項

- `--configuration new` 建立新的設定檔。

- `--configuration edit` 編輯現有的設定檔。

- `[--configfile <config filename>]` 是選擇性參數，允許自訂設定檔案名稱。

## <a name="configuration-file-for-snapshot-tools"></a>快照集工具的設定檔

您可以藉由執行來建立設定檔 `azacsnap -c configure --configuration new` 。  依預設，設定檔案名為 `azacsnap.json` 。  自訂檔案名可以與參數搭配使用 `--configfile=` (例如， `--configfile=<customname>.json`) 下列範例適用于 Azure 大型實例設定：

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>必要值的詳細資料

下列各節針對設定檔所需的各種值提供詳細的指引。

### <a name="sap-hana-values"></a>SAP Hana 值

將 *資料庫* 新增至設定時，需要下列值：

- **HANA 伺服器的位址** = SAP Hana Server 主機名稱或 IP 位址。
- **HANA SID** = SAP HANA 系統識別碼。
- **HANA 實例編號** = SAP Hana 實例號碼。
- **HANA HDB 使用者存放區金鑰** = 已設定執行資料庫備份許可權的 SAP Hana 使用者。

- 單一節點：節點的 IP 和主機名稱
- 具有 STONITH 的 HSR： IP 和節點的主機名稱
- 向外擴充 (N + N、N + M) ：目前的主要節點 IP 和主機名稱
- 沒有 STONITH 的 HSR： IP 和節點的主機名稱
- 單一節點上的多重 SID：裝載這些 Sid 之節點的主機名稱和 IP

### <a name="azure-large-instance-hli-storage-values"></a>Azure 大型實例 (的) 儲存體值

將資料 *存放* 區新增至資料庫區段時，需要下列值：

- **儲存體使用者名稱** = 此值是用來建立與儲存體之 SSH 連線的使用者名稱。
- **儲存體 IP 位址** = 存放裝置系統的位址。
- **存放磁片區名稱** = 要快照集的磁片區名稱。  這個值可以透過多種方式來決定，或許最簡單的方法是嘗試下列 shell 命令：

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Azure NetApp Files (ANF) 儲存體值

將 *ANF 儲存體* 新增至資料庫區段時，需要下列值：

- **服務主體驗證檔案名** = 這是在設定 `authfile.json` 與 Azure NetApp Files 儲存體進行通訊時，在 Cloud Shell 中產生的檔案。
- **完整 ANF 儲存體磁片區資源識別碼** = 要進行快照集之磁片區的完整資源識別碼。  您可以從下列來源抓取： Azure 入口網站– > ANF – > 磁片區– > 設定/屬性– > 資源識別碼

## <a name="configuration-file-overview-azacsnapjson"></a>設定檔案總覽 (`azacsnap.json`) 

在下列範例中， `azacsnap.json` 會使用一個 SID 來設定。

參數值必須設定為客戶的特定 SAP Hana 環境。
針對 **Azure 大型實例** 系統，這項資訊是由 Microsoft 服務管理在上線/移交通話期間提供，並可在切換期間提供的 Excel 檔案中取得。 如果您需要再次提供此資訊，請開啟服務要求。

以下是僅限範例，請據以更新所有的值。

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
              }
            ]
          }
        ],
        "anfStorage": []
      }
    }
  ]
}
```

> [!NOTE]
> 針對要在 DR 網站執行備份的 DR 案例，在 DR 設定檔中設定的 HANA 伺服器名稱 (例如， `DR.json` dr 網站上的) 應該與實際執行伺服器名稱相同。

> [!NOTE]
> 針對 Azure 大型實例，您的儲存體 IP 位址必須與您的伺服器集區位於相同的子網中。 例如，在此情況下，我們的伺服器集區子網為172。 18. 18. 0/24 和我們指派的儲存體 IP 是172.18.18.11。

## <a name="next-steps"></a>下一步

- [測試 Azure 應用程式一致的快照集工具](azacsnap-cmd-ref-test.md)
