---
title: 針對 Azure NetApp Files 使用 Azure 應用程式一致快照集工具的秘訣和訣竅 |Microsoft Docs
description: 提供使用可搭配 Azure NetApp Files 使用 Azure 應用程式一致快照集工具的秘訣和訣竅。
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 86d7b1a6d361ebbc0d8419d184f9a11d390a37f1
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803182"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 應用程式一致的快照集工具 (preview) 的秘訣和訣竅

本文提供當您使用 AzAcSnap 時，可能會有説明的秘訣和訣竅。

## <a name="limit-service-principal-permissions"></a>限制服務主體許可權

可能需要限制 AzAcSnap 服務主體的範圍。  如需 Azure 資源更細緻存取權管理的詳細資訊，請參閱 [AZURE RBAC 檔](https://docs.microsoft.com/azure/role-based-access-control/) 。  

以下是範例角色定義，其中包含 AzAcSnap 運作所需的最少必要動作。

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

## <a name="take-snapshots-manually"></a>手動取得快照集

執行任何備份命令 (`azacsnap -c backup`) 之前，請執行測試命令以檢查設定，並確認它們已順利執行。  這些測試的正確執行 `azacsnap` 可在 **Azure 大型實例** 或 **azure NetApp Files** 系統上，與已安裝的 SAP Hana 資料庫和 SAP Hana 的基礎儲存體系統進行通訊。

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

然後，若要手動執行資料庫快照集備份，請執行下列命令：

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>設定自動快照集備份

Unix/Linux 系統上的常見作法是在 `cron` 系統上使用自動執行命令。 快照集工具的標準作法是設定使用者的 `crontab` 。

`crontab` `azacsnap` 以下是讓使用者自動執行快照集的範例。

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

上述 crontab 的說明。

- `MAILTO=""`：藉由使用空白值，可防止 cron 在執行 crontab 專案時自動嘗試傳送電子郵件給使用者，因為它可能會在本機使用者的郵件檔案中。
- Crontab 專案的速記版本可一目了然：
  - `@monthly` = 每個月執行一次，也就是 "0 0 1 * *"。
  - `@weekly`  = 每週執行一次，也就是「0 0 * * 0」。
  - `@daily`   = 一天執行一次，也就是 "0 0 * * *"。
  - `@hourly`  = 一小時執行一次，也就是 "0 * * * *"。
- 前五個數據行用來指定時間，請參閱下列資料行範例：
  - `0,15,30,45`：每隔15分鐘
  - `0-23`：每小時
  - `*` ：每天
  - `*` ：每月
  - `*` ：每週的每一天
- 要執行的命令列包含在括弧 " ( # A1" 內
  - `. /home/azacsnap/.profile` = 提取使用者的. 設定檔以設定其環境，包括 $PATH 等等。
  - `cd /home/azacsnap/bin` = 將執行目錄變更至設定檔所在位置 "/home/azacsnap/bin"。
  - `azacsnap -c .....` = 要執行的完整 azacsnap 命令，包括所有選項。

如需 cron 和 crontab 檔案格式的進一步說明，請參閱： <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> 使用者必須負責監視 cron 作業，以確保能成功產生快照集。

## <a name="monitor-the-snapshots"></a>監視快照集

您應監視下列條件，以確保狀況良好的系統：

1. 可用磁碟空間。 快照集會保留較舊的磁片區塊，因此快照集會慢慢耗用磁碟空間。
    1. 若要協助將磁碟空間管理自動化，請使用 `--retention` 和 `--trim` 選項來自動清除舊的快照集和資料庫記錄檔。
1. 成功執行快照集工具
    1. 檢查檔案中是否 `*.result` 有最新執行的成功或失敗 `azacsnap` 。
    1. 檢查 `/var/log/messages` 命令的輸出 `azacsnap` 。
1. 定期將快照集還原至另一個系統，以達成一致性。

> [!NOTE]
> 若要列出快照詳細資料，請執行命令 `azacsnap -c details` 。

## <a name="delete-a-snapshot"></a>刪除快照集

若要刪除快照集，請執行命令 `azacsnap -c delete` 。 無法刪除作業系統層級的快照集。 您必須使用正確的命令 (`azacsnap -c delete`) 刪除儲存體快照集。

> [!IMPORTANT]
> 當您刪除快照集時，請務必小心。 一旦刪除後，就 **無法** 復原已刪除的快照集。

## <a name="restore-a-snapshot"></a>還原快照集

您可以將存放磁片區快照集還原至新的磁片區 (`-c restore --restore snaptovol`) 。  針對 Azure 大型實例，磁片區可還原為快照集 (`-c restore --restore revertvolume`) 。

> [!NOTE]
> 未提供 **任何** 資料庫復原命令。

您可以將快照集複製回 SAP Hana 資料區域，但是在 () 進行複製時，SAP Hana 不能正在執行 `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` 。

針對 Azure 大型實例，您可以藉由開啟服務要求，從現有可用的快照集還原所需的快照集，以聯繫 Microsoft 營運團隊。 您可以從 Azure 入口網站開啟服務要求： <https://portal.azure.com>

如果您決定執行嚴重損壞修復容錯移轉， `azacsnap -c restore --restore revertvolume` DR 網站上的命令會自動提供最新的 (`/hana/data` 和 `/hana/logbackups`) 磁片區快照集，以允許 SAP Hana 復原。 請小心使用此命令，因為它會中斷生產和 DR 網站之間的複寫。

## <a name="set-up-snapshots-for-boot-volumes-only"></a>只針對「開機」磁片區設定快照集

> [!IMPORTANT]
> 此操作只適用于 Azure 大型實例。

在某些情況下，客戶已經有可保護 SAP Hana 的工具，而且只想要設定「開機」磁片區快照集。  在此情況下，工作已簡化，應採取下列步驟。

1. 完成安裝先決條件的步驟1-4。
1. 啟用與儲存體的通訊。
1. 下載執行安裝程式以安裝快照工具。
1. 完成快照集工具的設定。
1. 建立新的設定檔，如下所示。 開機磁碟區詳細資料必須位於 <span style="color:red">紅色</span>) 的 OtherVolume stanza (使用者專案中：
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. 檢查設定檔案，請參閱下列範例：

    使用 `cat` 命令顯示設定檔的內容：

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
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

1. 測試開機磁碟區備份

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. 檢查它是否已列出，請注意新增 `--snapshotfilter` 選項以限制傳回的快照清單。

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    命令輸出：
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. 設定自動快照集備份。

> [!NOTE]
> 不需要設定與 SAP Hana 之間的通訊。

## <a name="restore-a-boot-snapshot"></a>還原「開機」快照集

> [!IMPORTANT]
> 這項操作適用于 Azure 大型實例只能。
> 伺服器將會還原到建立快照集的時間點。

您可以依照下列方式復原「開機」快照：

1. 客戶必須將伺服器關機。
1. 關閉伺服器之後，客戶必須開啟包含要還原之電腦識別碼和快照集的服務要求。
    > 客戶可以從 Azure 入口網站開啟服務要求： <https://portal.azure.com>
1. Microsoft 會使用指定的電腦識別碼和快照來還原作業系統 LUN，然後將伺服器開機。
1. 接著，客戶必須確認伺服器已開機且狀況良好。

還原之後不需執行其他步驟。

## <a name="key-facts-to-know-about-snapshots"></a>瞭解快照集的重要事實

儲存體磁片區快照集的重要屬性：

- **快照集的位置**：快照集可以在磁片區中的虛擬目錄中找到 (`.snapshot`) 。  請參閱下列 **Azure 大型實例** 範例：
  - 資料庫： `/hana/data/<SID>/mnt00001/.snapshot`
  - 共用： `/hana/shared/<SID>/.snapshot`
  - 日誌： `/hana/logbackups/<SID>/.snapshot`
  - 開機：從 OS 層級 **看不** 到的開機快照集，但可使用列出 `azacsnap -c details` 。

  > [!NOTE]
  >  `.snapshot` 是唯讀的隱藏 *虛擬* 資料夾，提供快照集的唯讀存取權。

- **最大快照集：** 硬體最多可承受每個磁片區250個快照集。 Snapshot 命令會根據命令列上的保留集，保留前置詞的最大快照集數目，如果超過最大值，則會刪除最舊的快照集。
- **快照集名稱：** 快照集名稱包含客戶所提供的前置詞標籤。
- **快照集的大小：** 取決於資料庫層級的大小/變更。
- **記錄檔位置：** 命令所產生的記錄檔會輸出到 JSON 設定檔中定義的資料夾中，預設為執行命令的子資料夾 (例如 `./logs`) 。

## <a name="next-steps"></a>後續步驟

- [疑難排解](azacsnap-troubleshoot.md)
