---
title: 設定 Azure SQL Edge (預覽)
description: 瞭解如何設定 Azure SQL Edge (preview) 。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 722d33e76b6009a44811dfcb8a3238b042ec6918
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816876"
---
# <a name="configure-azure-sql-edge-preview"></a>設定 Azure SQL Edge (預覽)

Azure SQL Edge 支援透過下列其中一個選項進行設定：

- 環境變數
- 放在/var/opt/mssql 資料夾中的 mssql 檔案

> [!NOTE]
> 設定環境變數會覆寫 mssql 檔案中指定的設定。

## <a name="configure-by-using-environment-variables"></a>使用環境變數進行設定

Azure SQL Edge 會公開數個不同的環境變數，其可用於設定 SQL Edge 容器。 這些環境變數是可用 Linux 上的 SQL Server 的子集。 如需 Linux 上的 SQL Server 環境變數的詳細資訊，請參閱 [環境變數](/sql/linux/sql-server-linux-configure-environment-variables/)。

下列 Linux 上的 SQL Server 環境變數不支援 Azure SQL Edge。 如果已定義此環境變數，則在容器初始化期間將會忽略這個環境變數。

| 環境變數 | 描述 |
|-----|-----|
| **MSSQL_ENABLE_HADR** | 啟用可用性群組。 例如，啟用 **1** ，並停用 **0** 。 |

> [!IMPORTANT]
> 適用於 SQL Edge 的 MSSQL_PID 環境變數只接受 **Premium** 和 **Developer** 作為有效值。 Azure SQL Edge 不支援使用產品金鑰進行初始化。

> [!NOTE]
> 下載適用于 Azure SQL Edge 的 [Microsoft 軟體授權條款](https://go.microsoft.com/fwlink/?linkid=2128283) 。

### <a name="specify-the-environment-variables"></a>指定環境變數

當您透過 [Azure 入口網站](deploy-portal.md)部署服務時，請指定 SQL Edge 的環境變數。 您可以將它們新增至模組部署的 **環境變數** 區段或作為 **容器建立選項**的一部分。

在 **環境變數**中加入值。

![使用環境變數清單進行設定](media/configure/set-environment-variables.png)

在 **容器建立選項**中新增值。

![使用容器建立選項設定](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>使用 mssql 檔案進行設定

Azure SQL Edge 不包含 [mssql 配置公用程式](/sql/linux/sql-server-linux-configure-mssql-conf/) ，例如 Linux 上的 SQL Server。 您必須手動設定 mssql 檔案，並將它放在對應至 SQL Edge 模組中/var/opt/mssql/資料夾的持續性存放磁片磁碟機中。 當您從 Azure Marketplace 部署 SQL Edge 時，此對應會指定為**容器建立選項**中的**裝載**選項。

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

下列 mssql 選項不適用於 SQL Edge：

|選項|描述|
|:---|:---|
|**客戶意見反應** | 選擇 SQL Server 是否會將意見反應傳送給 Microsoft。 |
|**Database mail 設定檔** | 為 Linux 上的 SQL Server 設定預設 Database Mail 設定檔。 |
|**高可用性** | 啟用可用性群組。 |
|**Microsoft 分散式交易協調器** | 設定和疑難排解 Linux 上的 MSDTC。 SQL Edge 不支援其他分散式交易相關的設定選項。 如需有關這些額外設定選項的詳細資訊，請參閱 [設定 MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)。 |
|**ML 服務 Eula** | 針對 Azure Machine Learning 套件接受 R 和 Python Eula。 只適用於 SQL Server 2019。|
|**outboundnetworkaccess** |針對[機器學習服務](/sql/linux/sql-server-linux-setup-machine-learning/) R、Python 及 Java 擴充功能啟用輸出網路存取。|

下列範例 mssql 檔案適用于 SQL Edge。 如需 mssql 檔案格式的詳細資訊，請參閱 [mssql 格式](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)。

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

## <a name="run-azure-sql-edge-as-non-root-user"></a>以非根使用者的身份執行 Azure SQL Edge

從 Azure SQL Edge CTP 2.2 開始，SQL Edge 容器可以使用非根使用者/群組來執行。 透過 Azure Marketplace 部署時，除非指定了不同的使用者/群組，否則 SQL Edge 容器會啟動為 mssql (非根) 使用者。 若要在部署期間指定不同的非根使用者，請 `*"User": "<name|uid>[:<group|gid>]"*` 在 [容器建立選項] 下新增機碼值組。 在下列範例中，SQL Edge 已設定為以使用者啟動 `*IoTAdmin*` 。

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

若要允許非根使用者存取裝載磁片區上的資料庫檔案，請確定您執行容器的使用者/群組具有持續性檔案儲存體的讀取 & 寫入權限。 在下列範例中，我們會將具有 user_id 10001 的非根使用者設定為檔案的擁有者。 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>從舊版 CTP 升級

先前的 Azure SQL Edge CTP 已設定為以根使用者的身份執行。 從舊版 CTP 升級時，可以使用下列選項

- 繼續使用根使用者-若要繼續使用根使用者，請 `*"User": "0:0"*` 在 [容器建立選項] 下新增機碼值組。
- 使用預設 mssql 使用者-若要使用預設 mssql 使用者，請遵循下列步驟
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
  - 更新容器建立選項，以指定 [ `*"User": "user_name | user_id*` 容器建立選項] 下的 [新增機碼/值組]。 請將 user_name 或 user_id 取代為您的 docker 主機上的實際 user_name 或 user_id。 
  - 變更目錄/裝入磁片區的許可權。

## <a name="persist-your-data"></a> 保存您的資料

即使您使用和重新開機容器，您的 Azure SQL Edge 設定變更和資料庫檔案仍會保存在容器中 `docker stop` `docker start` 。 不過，如果您移除容器，則 `docker rm` 會刪除容器中的所有專案，包括 AZURE SQL Edge 和您的資料庫。 下一節將說明如何使用**資料磁碟區**來保存您的資料庫檔案，即使已刪除相關聯的容器也一樣。

> [!IMPORTANT]
> 針對 Azure SQL Edge，請務必瞭解 Docker 中的資料持續性。 除了本節的討論，另請參閱 Docker 文件，以了解[如何管理 Docker 容器中的資料](https://docs.docker.com/engine/tutorials/dockervolumes/) \(英文\)。

### <a name="mount-a-host-directory-as-data-volume"></a>裝載主機目錄作為資料磁碟區

第一個選項是在主機上裝載目錄作為容器中的資料磁碟區。 若要執行此動作，請使用 `docker run` 命令搭配 `-v <host directory>:/var/opt/mssql` 旗標。 這可讓您在容器執行之間還原資料。

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge-developer
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge-developer
```

此技術也可讓您在 Docker 外部共用和檢視主機上的檔案。

> [!IMPORTANT]
> **Windows 上的 Docker** 的主機磁碟區對應，目前不支援對應完整 `/var/opt/mssql` 目錄。 不過，您可以將子目錄 (例如 `/var/opt/mssql/data`) 對應到您的主機電腦。

> [!IMPORTANT]
> 目前不支援使用 Azure SQL Edge 映射的 **Mac 上的 Docker** 的主機磁片區對應。 請改用資料磁碟區容器。 此限制是 `/var/opt/mssql` 目錄特有的。 從裝載的目錄讀取可以正常運作。 例如，您可以在 Mac 上使用 -v 來裝載主機目錄，並從位於主機的 .bak 檔案還原備份。

### <a name="use-data-volume-containers"></a>使用資料磁碟區容器

第二個選項是使用資料磁碟區容器。 您可以使用 `-v` 參數來指定磁碟區名稱而非主機目錄，藉以建立資料磁碟區容器。 下列範例會建立名為 **sqlvolume** 的共用資料磁碟區。

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge-developer
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge-developer
```

> [!NOTE]
> 這項在執行命令中隱含建立資料磁碟區的技術無法與舊版 Docker 搭配運作。 在此情況下，請使用 Docker 文件中概述的明確步驟，[建立和裝載資料磁碟區容器](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container) \(英文\)。

即使您停止並移除此容器，資料磁碟區仍會保存。 您可以使用 `docker volume ls` 命令來檢視它。

```bash
docker volume ls
```

如果您接著使用相同的磁片區名稱來建立另一個容器，新的容器會使用磁片區中包含的相同 Azure SQL Edge 資料。

若要移除資料磁碟區容器，請使用 `docker volume rm` 命令。

> [!WARNING]
> 如果您刪除資料磁片區容器，容器中的任何 Azure SQL Edge 資料都會 *永久* 刪除。


## <a name="next-steps"></a>後續步驟

- [連線到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 建立端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
