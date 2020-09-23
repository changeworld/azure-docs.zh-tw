---
title: 使用 Azure Data Studio 建立 Azure Arc 啟用的于 postgresql 超大規模
description: 使用 Azure Data Studio 建立 Azure Arc 啟用的于 postgresql 超大規模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: af0cdb814433b739b15d79bec9cb399cf0a2fef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934619"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>使用 Azure Data Studio 建立 Azure Arc 啟用的于 postgresql 超大規模

本檔將逐步引導您完成使用 Azure Data Studio 布建已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組的步驟。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>連接到 Azure Arc 資料控制器

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

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>建立已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組

1. 啟動 Azure Data Studio
1. 在 [連接] 索引標籤上，按一下左上方的三個點，然後選擇 [新增部署]。
1. 從部署選項中，選取 [ **于 postgresql 超大規模 server group-Azure Arc**
    >[!NOTE]
    > 如果目前未安裝 CLI，系統可能會提示您安裝 `azdata` CLI。
1. 接受隱私權和授權條款，然後按一下底部的 [ **選取** ]
1. 在 [部署于 postgresql 超大規模伺服器群組-Azure Arc] 分頁中，輸入下列資訊：
   - 輸入伺服器群組的名稱
   - 輸入並確認伺服器群組之 _postgres_ 系統管理員使用者的密碼
   - 針對資料選取適當的儲存體類別
   - 針對記錄選取適當的儲存類別
   - 選取適合備份的儲存類別
   - 選取要布建的背景工作節點數目
1. 按一下 [ **部署** ] 按鈕

這會開始在資料控制器上建立 Azure Arc 啟用的于 postgresql 超大規模伺服器群組。

幾分鐘後，您的建立應該就能順利完成。

## <a name="next-steps"></a>下一步
- [使用 Azure Data Studio 管理您的伺服器群組](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [監視您的伺服器群組](monitor-grafana-kibana.md)
- 閱讀適用於 PostgreSQL 的 Azure 資料庫超大規模的概念和操作指南，以將您的資料分散到多個于 postgresql 超大規模節點，並從 Azure Database for Postgres 超大規模的所有功能獲益。 :
    * [節點和資料表](../../postgresql/concepts-hyperscale-nodes.md)
    * [決定應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)
    * [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [散發和修改資料表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [設計多租使用者資料庫](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [設計即時分析儀表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 在上述檔中，略過登 **入 Azure 入口網站**的區段，& **建立適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) **。 在 Azure Arc 部署中執行其餘步驟。 這些區段僅適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供作為 Azure 雲端中的 PaaS 服務，但檔的其他部分直接適用于您 Azure Arc 啟用的于 postgresql 超大規模。

- [向外擴充您的適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
- [儲存體設定和 Kubernetes 儲存體概念](storage-configuration.md)
- [擴充永久性磁片區宣告](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 資源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

