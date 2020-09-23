---
title: Scale out 適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組
description: Scale out 適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e267a30d6f73b48f825c4b61b3bc1106133b8cdf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934031"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>藉由新增更多背景工作節點來向外擴充您的 Azure Arc 啟用的于 postgresql 超大規模伺服器群組
本檔說明如何向外擴充 Azure Arc 啟用的于 postgresql 超大規模伺服器群組。 它會引導您完成案例。 **如果您不想要執行此案例，而且想要瞭解如何向外延展，請跳至段落 [scale out](#scale-out)**。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>開始使用
如果您已經熟悉 Azure Arc enabled 于 postgresql 超大規模或適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 的調整模型，您可以跳過此段落。 如果不是，建議您在適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 的 [檔] 頁面中，開始閱讀有關此調整模型的資訊。 適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 是在 Azure 中以服務形式裝載的相同技術 (平臺即服務，也稱為 PAAS) ，而不是 Azure Arc 啟用的資料服務中提供：
- [節點和資料表](../../postgresql/concepts-hyperscale-nodes.md)
- [決定應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)
- [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)
- [散發和修改資料表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [設計多租使用者資料庫](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [設計即時分析儀表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* 在上述檔中，略過登 **入 Azure 入口網站**的區段，& **建立適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) **。 在 Azure Arc 部署中執行其餘步驟。 這些區段僅適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供作為 Azure 雲端中的 PaaS 服務，但檔的其他部分直接適用于您 Azure Arc 啟用的于 postgresql 超大規模。

## <a name="scenario"></a>狀況
此案例是指在 [建立已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組](create-postgresql-hyperscale-server-group.md) 檔中建立為範例的于 postgresql 超大規模伺服器群組。

### <a name="load-test-data"></a>載入測試資料
此案例使用公開可用的 GitHub 資料範例，可從 [Citus data 網站](https://www.citusdata.com/) 取得 (Citus 資料是 Microsoft) 的一部分。

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>連線到 Azure Arc 啟用的于 postgresql 超大規模伺服器群組

##### <a name="list-the-connection-information"></a>列出連接資訊
先取得連線資訊，以連接到 Azure Arc 啟用的于 postgresql 超大規模伺服器群組：此命令的一般格式為
```console
azdata arc postgres endpoint list -n <server name>
```
例如：
```console
azdata arc postgres endpoint list -n postgres01
```

範例輸出︰

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

##### <a name="connect-with-the-client-tool-of-your-choice"></a>使用您選擇的用戶端工具來連接。

執行下列查詢，以確認您目前有兩個 (或多個超大規模背景工作節點) ，分別對應至 Kubernetes pod：

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>建立範例架構
藉由執行下列查詢來建立兩個數據表：

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB 是于 postgresql 中二進位格式的 JSON 資料類型。 它會在單一資料行中儲存彈性的架構，並使用於 postgresql。 架構中將會有 GIN 索引，用來編制索引中的每個索引鍵和值。 使用 GIN 索引，可讓您快速且輕鬆地以該承載上的各種條件進行查詢。 我們會在載入資料之前，先建立幾個索引：

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

若要分區標準資料表，請針對每個資料表執行查詢。 指定我們想要分區的資料表，以及我們要分區的索引鍵。 我們將在 user_id 上分區 events 和 users 資料表：

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>載入範例資料
使用複製來載入資料 .。。從程式：

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>查詢資料
現在使用兩個節點來測量簡單查詢所需的時間：

```sql
SELECT COUNT(*) FROM github_events;
```
記下查詢執行時間。


## <a name="scale-out"></a>擴增
相應放大命令的一般格式為：
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

例如，執行下列命令，以將背景工作節點數目從2增加為4：
```console
azdata arc postgres server edit -n postgres01 -w 4
```

新增節點之後，您會看到伺服器群組的擱置狀態。 例如：
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

節點可供使用之後，超大規模分區 Rebalancer 會自動執行，並將資料轉散發至新的節點。 相應放大作業是一種線上作業。 當新增節點，並在節點之間重新分配資料時，資料仍可供查詢使用。

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>確認伺服器群組的新圖形 (選用) 
您可以使用下列其中一種方法，確認伺服器群組現在使用您新增的其他背景工作節點。

#### <a name="with-azdata"></a>使用 azdata：
執行命令：
```console
azdata arc postgres server list
```

它會傳回在命名空間中建立之伺服器群組的清單，並指出其背景工作節點的數目。 例如：
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>使用 kubectl：
執行命令：
```console
kubectl get postgresql-12
```

它會傳回在命名空間中建立之伺服器群組的清單，並指出其背景工作節點的數目。 例如：
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> **注意：** 如果您建立了11于 postgresql 版的伺服器群組，而不是12，請改為執行下列命令： _kubectl get 于 postgresql-11_

#### <a name="with-a-sql-query"></a>使用 SQL 查詢：
使用您選擇的用戶端工具連接到您的伺服器群組，然後執行下列查詢：

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>返回案例

如果您想要比較「選取計數」查詢的執行時間與範例資料集，請使用相同的「計數」查詢。 您可以跨四個背景工作節點使用它，而不需要在 SQL 語句中進行任何變更。

```sql
SELECT COUNT(*) FROM github_events;
```
請注意執行時間。


> [!NOTE]
> 視您的環境而定-例如，如果您已將測試伺服器群組部署 `kubeadm` 在單一節點 VM 上，您可能會在執行時間看到適度的改進。 若要深入瞭解您可以使用 Azure Arc 啟用的于 postgresql 超大規模來達到的效能改進類型，請觀看下列短片：
>* [使用 Azure 于 postgresql 超大規模 (Citus) 的高效能 HTAP ](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [使用 Python 建立 HTAP 應用程式 & Azure 于 postgresql 超大規模 (Citus) ](https://www.youtube.com/watch?v=YDT8_riLLs0)

> 預覽版本不支援相應縮小。 例如，您尚無法減少背景工作節點的數目。 如果您需要這樣做，您需要解壓縮/備份資料、卸載伺服器群組、使用較少的背景工作節點建立新的伺服器群組，然後再匯入資料。

## <a name="next-steps"></a>下一步

- 瞭解如何 [擴大和縮小 (記憶體、虛擬核心) Azure Arc 啟用的于 postgresql 超大規模伺服器群組](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- 瞭解如何在已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組中設定伺服器參數
- 閱讀適用於 PostgreSQL 的 Azure 資料庫超大規模的概念和操作指南，以將您的資料分散到多個于 postgresql 超大規模節點，並從 Azure Database for Postgres 超大規模的所有功能獲益。 :
    * [節點和資料表](../../postgresql/concepts-hyperscale-nodes.md)
    * [決定應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)
    * [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [散發和修改資料表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [設計多租使用者資料庫](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [設計即時分析儀表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* 在上述檔中，略過登 **入 Azure 入口網站**的區段，& **建立適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) **。 在 Azure Arc 部署中執行其餘步驟。 這些區段僅適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供作為 Azure 雲端中的 PaaS 服務，但檔的其他部分直接適用于您 Azure Arc 啟用的于 postgresql 超大規模。

- [儲存體設定和 Kubernetes 儲存體概念](storage-configuration.md)
- [擴充永久性磁片區宣告](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 資源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
