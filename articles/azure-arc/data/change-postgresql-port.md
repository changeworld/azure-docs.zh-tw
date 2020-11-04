---
title: 變更于 postgresql 埠
description: 變更 Azure Arc enabled 于 postgresql 超大規模伺服器群組正在接聽的埠。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328688"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>變更伺服器群組正在接聽的埠 

變更埠是伺服器群組的標準編輯操作。 為了變更埠，請執行下列命令：
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

例如，假設您的伺服器群組名稱是 _postgres01_ ，而您想要在埠 _866_ 上接聽。 您可以執行下列命令：
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>確認埠已變更

若要確認埠是否已變更，請執行下列命令以顯示伺服器群組的設定：
```console
azdata arc postgres server show -n <server group name>
```

在該命令的輸出中，查看您伺服器群組規格的 [服務] 區段中，針對專案「埠」顯示的埠號碼。
或者，您可以在伺服器群組規格的 [狀態] 區段的 [專案] externalEndpoint 中，確認 IP 位址後面接著您設定的埠號碼。

如圖所示，如果我們繼續上述範例，您會執行下列命令：
```console
azdata arc postgres server show -n postgres01
```

您會在這裡看到埠866參考：

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
和這裡

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>後續步驟
- 瞭解 [如何連接到您的伺服器群組](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)。
- 您可以在檔的 How-to\Manage\Configure & 規模一節中，瞭解如何設定伺服器群組的其他層面。
