---
title: 將 AdventureWorks 範例資料庫還原至 Azure Arc 啟用的于 postgresql 超大規模
description: 將 AdventureWorks 範例資料庫還原至 Azure Arc 啟用的于 postgresql 超大規模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b1ee779be118fcafd0efa2bd2718ece1c34c50d1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954323"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>將 AdventureWorks 範例資料庫還原至 Azure Arc 啟用的于 postgresql 超大規模

[AdventureWorks](/sql/samples/adventureworks-install-configure) 是範例資料庫，其中包含用於教學課程和範例的 OLTP 資料庫。 它是由 Microsoft 提供並維護，作為 [SQL Server 範例 GitHub 存放庫](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)的一部分。

開放原始碼專案已轉換 AdventureWorks 資料庫，使其與 Azure Arc 啟用的于 postgresql 超大規模相容。
- [原始專案](https://github.com/lorint/AdventureWorks-for-Postgres)
- [遵循將 CSV 檔案預先轉換成與于 postgresql 相容的專案](https://github.com/NorfolkDataSci/adventure-works-postgres)

本檔說明將 AdventureWorks 範例資料庫還原至您的于 postgresql 超大規模伺服器群組的簡單流程。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>下載 AdventureWorks 備份檔案

將 AdventureWorks .sql 檔案下載至您的于 postgresql 超大規模伺服器群組容器。 在此範例中，我們將使用 `kubectl exec` 命令，從遠端執行于 postgresql 超大規模伺服器群組容器中的命令，以將檔案下載到容器中。 您可以從可存取的任何位置下載此檔案 `curl` 。 如果您有想要在於 postgresql 超大規模伺服器群組容器中提取的其他資料庫備份檔案，請使用這個相同的方法。 一旦在於 postgresql 超大規模伺服器群組容器中，就可以輕鬆地建立資料庫、架構和填入資料。

執行如下所示的命令，以下載檔案來取代 pod 名稱和命名空間名稱的值，然後再執行它：

> [!NOTE]
>  您的容器必須有網際網路連線能力超過443，才能從 GitHub 下載檔案。

> [!NOTE]
>  使用 Postgres 超大規模伺服器群組的 [協調器] 節點的 pod 名稱。 其名稱為 <server group name> -0。  如果您不確定 pod 名稱，請執行命令 `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>步驟2：還原 AdventureWorks 資料庫

同樣地，您可以執行 kubectl exec 命令，以使用於 postgresql 超大規模伺服器群組容器中所含的 psql CLI 工具來建立和載入資料庫。

執行如下所示的命令，以建立空的資料庫，先替代 pod 名稱和命名空間名稱的值，再執行它。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

然後，執行如下的命令，以在執行之前先將 pod 名稱和命名空間名稱的值還原為資料庫。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **注意：在 Azure Arc 啟用的于 postgresql 超大規模上執行時，您將不會看到這麼多的效能優勢，除非您在向外延展，並在於 postgresql 超大規模伺服器群組的背景工作節點上分區/散發資料/資料表。請參閱 [建議的後續步驟](#suggested-next-steps)。**

## <a name="suggested-next-steps"></a>建議的後續步驟
- 閱讀適用於 PostgreSQL 的 Azure 資料庫超大規模的概念和操作指南，以將您的資料分散到多個于 postgresql 超大規模節點，並從適用於 PostgreSQL 的 Azure 資料庫超大規模的所有功能獲益。 :
    * [節點和資料表](../../postgresql/concepts-hyperscale-nodes.md)
    * [決定應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)
    * [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [散發和修改資料表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [設計多租使用者資料庫](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [設計即時分析儀表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* 在上述檔中，略過登 **入 Azure 入口網站** 的區段，& **建立適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus)**。 在 Azure Arc 部署中執行其餘步驟。 這些區段僅適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供作為 Azure 雲端中的 PaaS 服務，但檔的其他部分直接適用于您 Azure Arc 啟用的于 postgresql 超大規模。

- [擴增適用於 PostgreSQL 超大規模資料庫的 Azure 資料庫伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
