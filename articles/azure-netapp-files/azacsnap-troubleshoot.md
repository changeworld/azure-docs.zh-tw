---
title: 針對 Azure NetApp Files Azure 應用程式一致的快照集工具進行疑難排解 |Microsoft Docs
description: 提供使用可搭配 Azure NetApp Files 使用 Azure 應用程式一致快照集工具的疑難排解內容。
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632609"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>針對 Azure 應用程式一致的快照集工具 (預覽) 進行疑難排解

本文提供使用可搭配 Azure NetApp Files 使用 Azure 應用程式一致快照集工具的疑難排解內容。

以下是執行命令時可能會遇到的常見問題。 請依照所述的解決指示來修正問題。 如果您仍然遇到問題，請從 Azure 入口網站開啟服務要求，並將要求指派到 SAP Hana 大型實例佇列中，以供 Microsoft 支援服務回應。

## <a name="log-files"></a>記錄檔

使用 AzAcSnap 來偵測任何錯誤的最佳資訊來源之一，就是記錄檔。  

### <a name="log-file-location"></a>記錄檔位置

記錄檔會儲存在 `logPath` AzAcSnap 設定檔中每個參數所設定的目錄中。  預設的設定檔案名是 `azacsnap.json` ，預設值是， `logPath` `"./logs"` 這表示記錄檔會寫入至 `./logs` 相對於命令執行位置的目錄 `azacsnap` 。  讓 `logPath` 絕對位置 (例如 `/home/azacsnap/logs`) 可確保 `azacsnap` `/home/azacsnap/logs` 不論命令執行的位置，都會輸出記錄 `azacsnap` 。

### <a name="log-file-naming"></a>記錄檔命名

記錄檔檔案名是根據應用程式名稱 (例如 `azacsnap`) ， () 使用的命令選項 `-c` (例如 `backup` 、、等等 ) `test` 和設定 `details` 檔案名 (例如 default = `azacsnap.json`) 。  因此，如果使用 `-c backup` 命令，記錄檔檔案名預設會是 `azacsnap-backup-azacsnap.log` ，並寫入至所設定的目錄中 `logPath` 。  

建立此命名慣例是為了允許多個設定檔，每個資料庫各一個，並確保能夠輕鬆找到相關聯的記錄檔。  因此，如果設定檔案名是 `SID.json` ，則使用選項時的結果檔案名 `azacsnap -c backup --configfile SID.json` 將會是 `azacsnap-backup-SID.log` 。

### <a name="result-file-and-syslog"></a>結果檔和 syslog

`-c backup`命令選項 AzAcSnap 會使用命令寫出檔案 `*.result` 和系統記錄 (`/var/log/messages`) `logger` 。  `*.result`檔案名與[記錄](#log-file-naming)檔具有相同的基底名稱，並且與記錄檔放入相同的[位置](#log-file-location)。  每個範例都是簡單的一行輸出檔。

檔案的範例輸出 `*.result` 。
```output
Database # 1 (PR1) : completed ok
```

檔案的範例輸出 `/var/log/messages` 。
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>與 SAP Hana 的通訊失敗

使用執行測試來驗證與 SAP Hana 的通訊時 `azacsnap -c test --test hana` ，會提供下列錯誤：

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**解決方案：**

1. 檢查設定檔案 (例如， `azacsnap.json`) 每個 HANA 實例，以確保 SAP Hana 資料庫值都正確。
1. 請嘗試執行下列命令，以確認 `hdbsql` 命令是否在路徑中，而且可以連接到 SAP Hana 伺服器。 下列範例顯示命令的正確執行和其輸出。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    在此範例中， `hdbsql` 命令不在使用者中 `$PATH` 。

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    在此範例中， `hdbsql` 會暫時將命令新增至使用者 `$PATH` ，但當執行時顯示未使用命令正確設定連線金鑰 `hdbuserstore Set` (請參閱消費者入門指南，以取得詳細資料) ：

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > 若要永久加入使用者的 `$PATH` ，請更新使用者的檔案 `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`位置

設定與 SAP Hana 的通訊時， `hdbuserstore` 會使用此程式來建立安全通訊設定。  `hdbuserstore`程式通常位於 `/usr/sap/<SID>/SYS/exe/hdb/` 或下 `/usr/sap/hdbclient` 。  一般來說，安裝程式會將正確的位置新增至 `azacsnap` 使用者 `$PATH` 。

## <a name="failed-test-with-storage"></a>使用儲存體的測試失敗

命令 `azacsnap -c test --test storage` 未順利完成。

### <a name="azure-large-instance"></a>Azure 大型實例

下列範例是在 `azacsnap` Azure 大型實例上的 SAP Hana 上執行：

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**解決方案：** 當 Azure 大型實例儲存體使用者無法存取基礎儲存體時，上述錯誤通常會顯示。 若要使用提供的儲存體使用者來驗證儲存體的存取權，請執行 `ssh` 命令以驗證與儲存平臺的通訊。

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

具有預期輸出的範例：

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>無法建立主機 ' 172.18.18.11 (172.18.18.11) ' 的真實性

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**解決方案：** 請勿選取 [是]。 確定您的儲存體 IP 位址是正確的。 如果仍然有問題，請向 Microsoft operations 團隊確認儲存體 IP 位址。

### <a name="azure-netapp-files"></a>Azure NetApp Files

下列範例是在 `azacsnap` 使用 Azure NetApp Files 的 VM 上執行：

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**解決方案：**

1. 檢查服務主體檔案是否存在， `azureauth.json` 如設定檔中所設定 `azacsnap.json` 。
1. 檢查記錄檔 (例如， `logs/azacsnap-test-azacsnap.log`) 以查看服務主體 (`azureauth.json`) 是否有正確的內容。  記錄檔中的範例如下：

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. 檢查記錄檔 (例如， `logs/azacsnap-test-azacsnap.log`) 以查看服務主體 (`azureauth.json`) 是否已過期。 記錄檔中的範例如下：

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>下一步

- [提示](azacsnap-tips.md)
