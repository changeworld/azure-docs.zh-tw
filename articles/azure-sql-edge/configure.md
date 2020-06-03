---
title: 設定 Azure SQL Edge (預覽)
description: 深入了解如何設定 Azure SQL Edge (預覽)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a28724e00f59fe049d1d9d6dfbcbc5a3f9556124
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235153"
---
# <a name="configure-azure-sql-edge-preview"></a>設定 Azure SQL Edge (預覽)

Azure SQL Edge 支援透過下列其中一個選項進行設定：

- 使用環境變數。
- 使用放在 /var/opt/mssql 資料夾中的 mssql.conf 檔案。

> [!NOTE]
> 設定環境變數時會覆寫 mssql.conf 檔案中指定的設定。

## <a name="configure-using-environment-variables"></a>使用環境變數進行設定

Azure SQL Edge 會公開數個不同的環境變數，其可用於設定 SQL Edge 容器。 這些環境變數是可供 Linux 上的 SQL Server 使用的環境變數子集。 如需 Linux 上的 SQL Server 環境變數詳細資訊，請參閱[環境變數](/sql/linux/sql-server-linux-configure-environment-variables/)。

Azure SQL Edge 不支援下列 Linux 上的 SQL Server 環境變數。 若已定義，則會在容器初始化期間忽略這些環境變數。

| 環境變數 | 描述 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 啟用可用性群組。 例如 '1' 為啟用，'0' 為停用 |

> [!IMPORTANT]
> 適用於 SQL Edge 的 MSSQL_PID 環境變數只接受 **Premium** 和 **Developer** 作為有效值。 Azure SQL Edge 不支援使用產品金鑰進行初始化。

> [!NOTE]
> 若要下載 Azure SQL Edge 終端使用者授權合約，請參閱[終端使用者授權合約](https://go.microsoft.com/fwlink/?linkid=2128283)。

### <a name="specifying-the-environment-variables"></a>指定環境變數

透過 [Azure 入口網站](deploy-portal.md)部署 Azure SQL Edge 時，可以指定 SQL Edge 的環境變數。 這可以在模組部署的 [環境變數] 區段中新增，或成為容器建立選項的一部分，如下所述。

*使用環境變數選項進行設定*

![使用環境變數清單進行設定](media/configure/set-environment-variables.png)

*使用容器建立選項進行設定*

![使用容器建立選項進行設定](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>使用 mssql.conf 檔案進行設定

如同 Linux 上的 SQL Server，Azure SQL Edge 不包含 [mssql-conf 設定公用程式](/sql/linux/sql-server-linux-configure-mssql-conf/)，因此必須手動設定 mssql.conf 檔案並將其放在與 SQL Edge 模組中的 /var/opt/mssql/ 資料夾對應的永續性儲存體磁碟機中。 從 Azure Marketplace 部署 SQL Edge 時，會將此對應指定為容器建立選項中的 [掛接] 選項

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

下列 mssql.conf 選項不適用於 SQL Edge：

|選項|描述|
|:---|:---|
|**客戶意見反應** | 選擇 SQL Server 是否要將意見反應傳送給 Microsoft。 |
|**Database Mail 設定檔** | 為 Linux 上的 SQL Server 設定預設 Database Mail 設定檔。 |
|**高可用性** | 啟用可用性群組。 |
|**Microsoft 分散式交易協調器** | 設定和疑難排解 Linux 上的 MSDTC。 SQL Edge 也不支援其他與分散式交易相關的設定選項。 如需這些額外設定選項的詳細資訊，請參閱[設定 MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices EULA** | 接受適用於機器學習服務套件的 R 和 Python EULA。 只適用於 SQL Server 2019。|
|**outboundnetworkaccess** |針對[機器學習服務](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 及 Java 擴充功能啟用輸出網路存取。|

以下提供適用時 SQL Edge 的範例 mssql.conf 檔案。 如需 mssql.conf 檔案的格式詳細資訊，請參閱 [mssql.conf 格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

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

## <a name="next-step"></a>後續步驟

- [連線到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
