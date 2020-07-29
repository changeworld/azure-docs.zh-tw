---
title: 設定 Azure SQL Edge (預覽)
description: 瞭解如何設定 Azure SQL Edge （預覽）。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636235"
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
|**MLServices EULA** | 接受適用于 Azure Machine Learning 套件的 R 和 Python Eula。 只適用於 SQL Server 2019。|
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

## <a name="next-steps"></a>後續步驟

- [連線到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 建立端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
