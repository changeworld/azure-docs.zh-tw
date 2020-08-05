---
title: 設定 Azure SQL Edge (預覽)
description: 瞭解如何設定 Azure SQL Edge (預覽) 。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551977"
---
# <a name="configure-azure-sql-edge-preview"></a>設定 Azure SQL Edge (預覽)

Azure SQL Edge 支援透過下列其中一個選項進行設定：

- 環境變數
- 放在/var/opt/mssql 資料夾中的 mssql. 會議檔案

> [!NOTE]
> 設定環境變數會覆寫 mssql 檔案中指定的設定。

## <a name="configure-by-using-environment-variables"></a>使用環境變數進行設定

Azure SQL Edge 會公開數個不同的環境變數，其可用於設定 SQL Edge 容器。 這些環境變數是可供 Linux 上的 SQL Server 的子集。 如需 Linux 上的 SQL Server 環境變數的詳細資訊，請參閱[環境變數](/sql/linux/sql-server-linux-configure-environment-variables/)。

Azure SQL Edge 不支援下列 Linux 上的 SQL Server 環境變數。 若已定義，則會在容器初始化期間忽略此環境變數。

| 環境變數 | 描述 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 啟用可用性群組。 例如， **1**已啟用， **0**則停用。 |

> [!IMPORTANT]
> 適用於 SQL Edge 的 MSSQL_PID 環境變數只接受 **Premium** 和 **Developer** 作為有效值。 Azure SQL Edge 不支援使用產品金鑰進行初始化。

> [!NOTE]
> 下載 Azure SQL Edge 的[Microsoft 軟體授權條款](https://go.microsoft.com/fwlink/?linkid=2128283)。

### <a name="specify-the-environment-variables"></a>指定環境變數

當您透過[Azure 入口網站](deploy-portal.md)部署服務時，請指定 SQL Edge 的環境變數。 您可以在模組部署的 [**環境變數**] 區段中新增它們，或做為**容器建立選項**的一部分。

在**環境變數**中加入值。

![使用環境變數清單設定](media/configure/set-environment-variables.png)

在 [**容器建立選項**] 中新增值。

![使用容器建立選項設定](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>使用 mssql ... 檔案進行設定

Azure SQL Edge 並未包含[mssql 會議設定公用程式](/sql/linux/sql-server-linux-configure-mssql-conf/)，如 Linux 上的 SQL Server 執行。 您必須手動設定 mssql 檔案，並將它放在對應至 SQL Edge 模組中/var/opt/mssql/資料夾的持續性存放磁片磁碟機中。 當您從 Azure Marketplace 部署 SQL Edge 時，會在 [**容器建立選項**] 中將此對應指定為 [**裝載**] 選項。

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

下列 mssql. 會議選項不適用於 SQL Edge：

|選項|描述|
|:---|:---|
|**客戶意見反應** | 選擇 SQL Server 是否將意見反應傳送給 Microsoft。 |
|**Database mail 設定檔** | 為 Linux 上的 SQL Server 設定預設 Database Mail 設定檔。 |
|**高可用性** | 啟用可用性群組。 |
|**Microsoft 分散式交易協調器** | 設定和疑難排解 Linux 上的 MSDTC。 SQL Edge 不支援其他與分散式交易相關的設定選項。 如需這些額外設定選項的詳細資訊，請參閱[CONFIGURE MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)。 |
|**ML 服務 Eula** | 接受適用于 Azure Machine Learning 套件的 R 和 Python Eula。 只適用於 SQL Server 2019。|
|**outboundnetworkaccess** |針對[機器學習服務](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 及 Java 擴充功能啟用輸出網路存取。|

下列範例 mssql. 會議檔案適用于 SQL Edge。 如需 mssql. 會議檔案格式的詳細資訊，請參閱[mssql. 會議格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>以非根使用者身分執行 Azure SQL Edge

從 Azure SQL Edge CTP 2.2 開始，SQL Edge 容器可以使用非根使用者/群組來執行。 透過 Azure Marketplace 部署時，除非指定了不同的使用者/群組，否則 SQL Edge 容器會以 mssql (非根) 使用者的身分啟動。 若要在部署期間指定不同的非根使用者，請 `*"User": "<name|uid>[:<group|gid>]"*` 在 [容器建立選項] 下新增索引鍵/值組。 在下列範例中，SQL Edge 已設定為以使用者身分啟動 `*IoTAdmin*` 。

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

若要允許非根使用者存取已掛接之磁片區上的資料庫檔案，請確定您執行容器的使用者/群組具有持續性檔案儲存體的讀取 & 寫入權限。 在下列範例中，我們將具有 user_id 10001 的非根使用者設定為檔案的擁有者。 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>從先前的 CTP 版本升級

先前的 Azure SQL Edge CTP 已設定為以根使用者身分執行。 從舊版 CTP 升級時，可以使用下列選項：

- 繼續使用根使用者-若要繼續使用根使用者，請 `*"User": "0:0"*` 在 [容器建立選項] 下新增機碼值組。
- 使用預設的 mssql 使用者-若要使用預設的 mssql 使用者，請遵循下列步驟
  - 在 docker 主機上新增名為 mssql 的使用者。 在下列範例中，我們會新增識別碼為10001的使用者 mssql。 此使用者也會新增至根群組。
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - 變更資料庫檔案所在目錄/裝載磁片區上的許可權 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- 使用不同的非根使用者帳戶-使用不同的非根使用者帳戶
  - 更新容器建立選項，以指定 [ `*"User": "user_name | user_id*` 容器建立選項] 下的 [新增索引鍵/值組]。 請以您的 docker 主機實際 user_name 或 user_id 取代 user_name 或 user_id。 
  - 變更目錄/裝載磁片區上的許可權。



## <a name="next-steps"></a>後續步驟

- [連線到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 建立端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
