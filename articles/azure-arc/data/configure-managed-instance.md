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
ms.openlocfilehash: 37ba4f10365fca4292171c3bd2d9a3e7d00045bb
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985864"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>設定 Azure Arc 啟用的 SQL 受控實例

本文說明如何設定 Azure Arc 啟用的 SQL 受控實例。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Configure resources

### <a name="configure-using-azure-data-cli-azdata"></a>設定使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

您可以使用來編輯 Azure Arc 啟用之 SQL 受控實例的設定 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 。 執行下列命令以查看設定選項。 

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

1. 將 `mssql-custom.conf` 檔案複製到 `master-0` Pod 中 `arc-sqlmi` 容器的 `/var/opt/mssql` 內。 取代為 `<namespaceName>` 此實例部署所在的命名空間。

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. 重新開機 SQL 受控實例。  取代為 `<namespaceName>` 此實例部署所在的命名空間。

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**已知的限制**
- 上述步驟需要 Kubernetes 叢集系統管理員權限
- 這可能會在預覽期間變更
