---
title: 設定 Azure Arc 啟用的 SQL 受控實例
description: 設定 Azure Arc 啟用的 SQL 受控實例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936723"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>設定 Azure Arc 啟用的 SQL 受控實例

本文說明如何設定 Azure Arc 啟用的 SQL 受控實例。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>設定 Azure Arc 啟用的 SQL 受控執行個體的資源

### <a name="configure-using-azdata"></a>使用 azdata 設定

您可以使用 CLI 來編輯 Azure Arc 啟用 SQL 受控實例的設定 `azdata` 。 執行下列命令以查看設定選項。 

```
azdata arc sql mi edit --help
```

下列範例會設定 cpu 核心和記憶體要求和限制。

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

若要查看對 SQL 受控實例所做的變更，您可以使用下列命令來查看設定 yaml 檔：

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>設定伺服器選項

您可以在建立之後設定 Azure Arc 啟用的 SQL 受控實例的伺服器設定。 本文說明如何設定啟用或停用 mssql 代理程式等設定，以啟用特定追蹤旗標以進行疑難排解。

若要變更這些設定的任一個，請遵循下列步驟：

1. 建立包含目標設定的自訂 `mssql-custom.conf` 檔案。 下列範例會啟用 SQL Agent，並啟用追蹤旗標1204。：

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. 將 `mssql-custom.conf` 檔案複製到 `master-0` Pod 中 `mssql-miaa` 容器的 `/var/opt/mssql` 內。 將 `<namespaceName>` 取代為巨量資料叢集名稱。

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. 將 SQL Server 執行個體重新啟動。  將 `<namespaceName>` 取代為巨量資料叢集名稱。

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**已知限制**
- 上述步驟需要 Kubernetes 叢集系統管理員權限
- 這可能會在預覽期間變更
