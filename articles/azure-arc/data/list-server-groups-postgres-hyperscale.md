---
title: 列出在 Azure Arc 資料控制器中建立的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組
description: 列出在 Azure Arc 資料控制器中建立的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936801"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>列出在 Azure Arc 資料控制器中建立的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組

本文說明如何取得在 Arc 資料控制器中建立的伺服器群組清單。

若要取得這份清單，請在連接到 Arc 資料控制器之後，使用下列其中一種方法：

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>從具有 azdata 的 CLI
命令的一般格式為：
```console
azdata arc postgres server list
```

它會傳回輸出，如下所示：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
如需此命令可用參數的詳細資訊，請執行：
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>從具有 kubectl 的 CLI
執行下列其中一個命令。

**若要列出伺服器群組而不考慮 Postgres 的版本，請執行：**
```console
kubectl get postgresqls
```
它會傳回輸出，如下所示：
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**若要列出特定 Postgres 版本的伺服器群組，請執行：**
```console
kubectl get postgresql-12
```

若要列出執行 11 Postgres 版的伺服器群組，請將 _于 postgresql-12_ 取代為 _于 postgresql-11_。

## <a name="next-steps"></a>後續步驟：

* [閱讀相關文章，以瞭解如何取得連接端點，以及如何形成連接字串以連接到您的伺服器群組](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [閱讀有關顯示 Azure Arc 啟用的于 postgresql 超大規模伺服器群組設定的文章](show-configuration-postgresql-hyperscale-server-group.md)
