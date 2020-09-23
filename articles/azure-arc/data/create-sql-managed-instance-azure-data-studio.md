---
title: 使用 Azure Data Studio 建立 Azure SQL 受控實例
description: 使用 Azure Data Studio 建立 Azure SQL 受控實例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 989496885445a8a0a8d3bbc1a789975a2875c6e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934615"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>使用 Azure Data Studio 建立 SQL 受控實例-Azure Arc

本檔將逐步引導您使用 Azure Data Studio 來安裝 Azure SQL 受控實例 Azure Arc 的步驟

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>登入 Azure Arc 資料控制器

如果您尚未登入，請先登入 Azure Arc 資料控制器，才能建立實例。

```console
azdata login
```

然後，系統會提示您輸入建立資料控制器的命名空間，以及登入控制器的使用者名稱和密碼。  

> 如果您需要驗證命名空間，您可以執行 ```kubectl get pods -A``` 以取得叢集中的所有命名空間清單。

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>在 Azure Arc 上建立 Azure SQL 受控實例

- 啟動 Azure Data Studio
- 在 [連接] 索引標籤上，按一下左上方的三個點，然後選擇 [新增部署]。
- 從部署選項中，選取 [ **AZURE SQL 受控實例-Azure Arc** 
  > **注意：** 如果目前未安裝 azdata CLI，系統可能會提示您安裝。
- 接受隱私權和授權條款，然後按一下底部的 [ **選取** ]



- 在 [部署 Azure SQL 受控實例-Azure Arc] 分頁中，輸入下列資訊：
  - 輸入 SQL Server 實例的名稱
  - 輸入並確認 SQL Server 實例的密碼
  - 針對資料選取適當的儲存體類別
  - 針對記錄選取適當的儲存類別

- 按一下 [ **部署** ] 按鈕

- 這應該會開始在資料控制器上建立 Azure SQL 受控實例 Azure Arc。

- 幾分鐘後，您的建立應該會成功完成

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>從 Azure Data Studio 連接到 Azure SQL 受控實例-Azure Arc

- 藉由提供資料控制器的命名空間、使用者名稱和密碼，登入 Azure Arc 資料控制器： 
```console
azdata login
```

- 使用下列命令來查看已布建的所有 Azure SQL 受控實例：

```console
azdata arc sql mi list
```

輸出應該看起來像這樣，請複製 ServerEndpoint (包括) 的埠號碼。

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- 在 Azure Data Studio 的 [**連接**] 索引標籤下，按一下 [**伺服器**] 視圖上的 [**新增連接**]。
- 在 [ **連接** ] 分頁中，將 ServerEndpoint 貼到 [伺服器] 文字方塊中。
- 選取 [ **SQL 登** 入] 作為驗證類型
- 輸入 *sa* 作為使用者名稱
- 輸入帳戶的密碼 `sa`
- （選擇性）輸入要連接的特定資料庫名稱
- （選擇性）選取/新增適當的伺服器群組
- 選取 **[連線] 以** 連線至 Azure SQL 受控實例-Azure Arc




## <a name="next-steps"></a>後續步驟

現在，請嘗試 [監視您的 SQL 實例](monitor-grafana-kibana.md)
