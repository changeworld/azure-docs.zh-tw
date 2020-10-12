---
title: 將資料從于 postgresql 資料庫移轉至已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組
titleSuffix: Azure Arc enabled database services
description: 將資料從于 postgresql 資料庫移轉至已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 521fd61f18d6673e21c23dbca4cfc12d2ee4bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934526"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>將于 postgresql 資料庫移轉至啟用 Azure Arc 的于 postgresql 超大規模伺服器群組

本檔說明如何將現有的于 postgresql 資料庫 (未裝載于 Azure Arc 啟用的資料服務) 中的步驟，到 Azure Arc 啟用的于 postgresql 超大規模伺服器群組。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>考量

Azure Arc 啟用的于 postgresql 超大規模伺服器群組是于 postgresql 的版本，並且會在啟用 CitusData 擴充功能的情況下執行。 因此，任何適用于在 Azure Arc 外于 postgresql 的工具，都應該能夠使用 Azure Arc 啟用的于 postgresql 超大規模伺服器群組。


如此一來，您目前使用的工具組可進行 Postgres，您應該能夠：
1. 從 Azure Arc 外部託管的實例備份您的 Postgres 資料庫
2. 在已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組中還原它

剩下的工作就是：
- 重設伺服器參數
- 重設安全性內容：重新建立使用者、角色和重設許可權 .。。

若要執行這項備份/還原作業，您可以使用任何能夠執行 Postgres 備份/還原的工具。 例如：
- Azure Data Studio 及其 Postgres 延伸模組
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>範例
讓我們使用此工具來說明這些步驟 `pgAdmin` 。
請考慮下列設定：
- **來源：**  
    在裸機伺服器上執行的 Postgres 伺服器，以及名為 JEANYDSRV 的內部部署。 它是12版，並裝載名為 MyOnPremPostgresDB 的資料庫，其中有一個具有1個數據列:::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="遷移來源":::的資料表 T1

- **目的地：**  
    在 Azure Arc 環境中執行且名為 postgres01 的 Postgres 伺服器。 這是12版。 除了標準 Postgres 資料庫之外，它沒有任何資料庫。  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="遷移來源":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>在內部部署中進行源資料庫的備份

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="遷移來源":::

進行設定：
1. 請為其指定檔案名： **MySourceBackup**
2. 將格式設定為**自訂** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="遷移來源":::設定

備份成功完成：  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="遷移來源":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組中的目的地系統上建立空白資料庫

> [!NOTE]
> 若要在工具中註冊 Postgres 實例 `pgAdmin` ，您必須在 Kubernetes 叢集中使用實例的公用 IP，並適當地設定埠和安全性內容。 執行下列命令之後，您將會在端點行上找到這些詳細資料 `psql` ：

```console
azdata arc postgres endpoint list -n postgres01
```
這會傳回如下的輸出：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

讓我們命名目的地資料庫 **RESTORED_MyOnPremPostgresDB**  
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="遷移-目的地-資料庫-建立"lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>還原 Arc 設定中的資料庫
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="遷移來源":::

設定還原：
1. 指向包含要還原之備份的檔案： **MySourceBackup**
2. 將格式設定為 [**自訂] 或 [tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="遷移來源":::設定]

3. 按一下 [還原]。  

   還原成功。  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="遷移來源":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>確認已成功在您 Azure Arc 啟用的于 postgresql 超大規模伺服器群組中還原資料庫

使用下列其中一種方法：

**來源 `pgAdmin` ：**  

展開 Azure Arc 安裝程式中主控的 Postgres 實例。 您將會在資料庫中看到已還原的資料表，而當您選取資料時，它會顯示與內部部署實例相同的資料列：

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="遷移來源"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. 從您的 `psql` 連接字串中，使用 `-d` 參數表示資料庫名稱。 使用下列命令時，系統會提示您輸入密碼：

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` 連接。

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. 選取資料表，您將會看到您從內部部署 Postgres 實例還原的資料：

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - 在 Azure Arc 啟用的于 postgresql 超大規模上執行時，您將不會看到這麼多的效能優勢，除非您在向外延展，並在於 postgresql 超大規模伺服器群組的背景工作節點上分區/散發資料。 請參閱 [後續步驟](#next-steps)。
>
> - 目前無法「上線至 Azure Arc」在內部部署或任何其他雲端中執行的現有 Postgres 實例。 換句話說，您無法在現有的 Postgres 實例上安裝某種「Azure Arc 代理程式」，讓它成為 Azure Arc 啟用的 Postgres 設定。相反地，您必須建立新的 Postgres 實例，並將資料傳輸至其中。 您可以使用上面所示的技巧來執行這項作業，或者您可以使用任何您選擇的 ETL 工具。

## <a name="next-steps"></a>後續步驟

- 閱讀適用於 PostgreSQL 的 Azure 資料庫超大規模的概念和操作指南，以將您的資料分散到多個于 postgresql 超大規模節點，並從適用於 PostgreSQL 的 Azure 資料庫超大規模的所有功能獲益：
    * [節點和資料表](../../postgresql/concepts-hyperscale-nodes.md)
    * [決定應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)
    * [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [散發和修改資料表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [設計多租使用者資料庫](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [設計即時分析儀表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * 在這些檔中，略過登 **入 Azure 入口網站**的區段，並 **建立 Azure Database For Postgres-超大規模 (Citus) **。 在 Azure Arc 部署中執行其餘步驟。 這些區段僅適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供作為 Azure 雲端中的 PaaS 服務，但檔的其他部分直接適用于您 Azure Arc 啟用的于 postgresql 超大規模。

- [擴增適用於 PostgreSQL 超大規模資料庫的 Azure 資料庫伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
