---
title: 在 Azure Arc 上建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組
description: 在 Azure Arc 上建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7fdc367e3db298b60dc9a15453d58a738c13274a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108298"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>建立已啟用 Azure Arc 的 PostgreSQL 超大規模資料庫伺服器群組

本檔說明在 Azure Arc 上建立于 postgresql 超大規模伺服器群組的步驟。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>開始使用
如果您已經熟悉下列主題，可以略過此段落。
在繼續建立之前，您可能會想要閱讀一些重要的主題：
- [Azure Arc 已啟用資料服務的總覽](overview.md)
- [連線模式和需求](connectivity.md)
- [儲存體設定和 Kubernetes 儲存體概念](storage-configuration.md)
- [Kubernetes 資源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

如果您想要在不自行布建完整環境的情況下嘗試進行，請在 Azure Kubernetes Service (AKS) 、AWS 彈性 Kubernetes 服務 (EKS) 、Google Cloud Kubernetes 引擎 (GKE) 或 Azure VM 中快速開始使用 [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 快速入門。


## <a name="login-to-the-azure-arc-data-controller"></a>登入 Azure Arc 資料控制器

您必須先登入 Azure Arc 資料控制器，才能建立實例。 如果您已經登入資料控制器，可以略過此步驟。

```console
azdata login
```

系統會提示您輸入使用者名稱、密碼和系統命名空間。  

> 如果您使用腳本來建立資料控制器，則您的命名空間應該是 **弧線**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>僅 OpenShift 使用者的初步和暫存步驟

請先執行此步驟，再移至下一個步驟。 若要將于 postgresql 超大規模伺服器群組部署到非預設專案中的 Red Hat OpenShift，您需要對叢集執行下列命令，以更新安全性條件約束。 此命令會將所需的許可權授與將執行您的于 postgresql 超大規模伺服器群組的服務帳戶。 安全性內容條件約束 (SCC) **_arc-資料 scc_** 是您在部署 Azure Arc 資料控制器時所加入的條件約束。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**伺服器組名** 是您將在下一個步驟中建立的伺服器組名。_
   
如需 OpenShift 中 SCCs 的詳細資訊，請參閱 [OpenShift 檔](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。
您現在可以執行下一個步驟。

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組

若要在 Azure Arc 上建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組，請使用下列命令：

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **還有其他可用的命令列參數。 執行，以查看完整的選項清單 `azdata arc postgres server create --help` 。**
> - 用於備份的儲存類別 (_--儲存類別備份-scb_) 預設為數據控制器的資料儲存類別（如果未提供）。
> - --磁片區大小-* 參數所接受的單位是 Kubernetes 的資源數量 (整數，後面接著下列其中一個 SI 尾碼 (T、G、M、K、M) 或其對等的兩個對等 (Ti、Gi、Mi、Ki) # A5。
> - 名稱長度必須少於或等於12個字元，且必須符合 DNS 命名慣例。
> - 系統會提示您輸入 _postgres_ standard 系統管理使用者的密碼。  您可以在 `AZDATA_PASSWORD` 執行 create 命令之前，先設定會話環境變數，以略過互動式提示字元。
> - 如果您在相同的終端機會話中使用 AZDATA_USERNAME 和 AZDATA_PASSWORD 會話環境變數來部署資料控制器，則也會使用 AZDATA_PASSWORD 的值來部署于 postgresql 超大規模伺服器群組。 如果您想要使用其他密碼，請 (1) 更新 AZDATA_PASSWORD 或 (2 的值) 刪除 AZDATA_PASSWORD 環境變數或刪除其值會在您建立伺服器群組時，以互動方式提示輸入密碼。
> - 于 postgresql 超大規模資料庫引擎的預設系統管理員使用者名稱是 _postgres_ ，此時無法變更。
> - 建立于 postgresql 超大規模伺服器群組將不會在 Azure 中立即註冊資源。 在將 [資源清查](upload-metrics-and-logs-to-azure-monitor.md)  或 [使用量資料](view-billing-data-in-azure.md) 上傳至 azure 的過程中，會在 azure 中建立資源，而且您將能夠在 Azure 入口網站中看到您的資源。



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>列出在 Arc 設定中建立的適用於 PostgreSQL 的 Azure 資料庫伺服器群組

若要在 Azure Arc 上查看于 postgresql 超大規模伺服器群組，請使用下列命令：

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>取得端點以連接到您的適用於 PostgreSQL 的 Azure 資料庫伺服器群組

若要查看于 postgresql 實例的端點，請執行下列命令：

```console
azdata arc postgres endpoint list -n <server group name>
```
例如：
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

您可以使用於 postgresql 實例端點，從您最愛的工具連接到于 postgresql 超大規模伺服器群組：  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)、 [Pgcli](https://www.pgcli.com/) psql、pgAdmin 等。

如果您使用 Azure VM 進行測試，請依照下列指示執行：

## <a name="special-note-about-azure-virtual-machine-deployments"></a>關於 Azure 虛擬機器部署的特殊注意事項

當您使用 Azure 虛擬機器時，端點 IP 位址不會顯示 _公用_ IP 位址。 若要找出公用 IP 位址，請使用下列命令：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然後，您可以將公用 IP 位址與埠結合，以建立您的連接。

您可能還需要透過網路安全性閘道 (NSG) 來公開于 postgresql 超大規模伺服器群組的埠。 若要允許流量通過 (NSG) 您需要新增規則，您可以使用下列命令來執行此動作：

若要設定規則，您將需要知道 NSG 的名稱。 您可以使用下列命令來判斷 NSG：

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

擁有 NSG 的名稱之後，您就可以使用下列命令來新增防火牆規則。 此處的範例值會建立埠30655的 NSG 規則，並允許來自 **任何** 來源 IP 位址的連接。  這並不是安全性最佳做法！  您可以藉由指定您的用戶端 IP 位址專屬的-來源位址首碼值，或是涵蓋您小組或組織 IP 位址的 IP 位址範圍，來更妥善地鎖定專案。

將下列--destination-port-範圍參數的值取代為您從上述 ' azdata arc postgres server list ' 命令取得的埠號碼。

```console
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>與 Azure Data Studio 連線

開啟 Azure Data Studio，並使用上述的外部端點 IP 位址和埠號碼連線至您的實例，以及您在建立實例時指定的密碼。  如果 [連線 *類型* ] 下拉式清單中無法使用於 postgresql，您可以在 [擴充功能] 索引標籤中搜尋于 postgresql，以安裝于 postgresql 擴充功能。

> [!NOTE]
> 您將需要按一下 [連線] 面板中的 [Advanced] 按鈕，以輸入埠號碼。

請記住，如果您使用 Azure VM，您將需要可透過下列命令存取的 _公用_ IP 位址：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>使用 psql 連接

若要存取您的于 postgresql 超大規模伺服器群組，請傳遞您從上述取出的于 postgresql 超大規模伺服器群組的外部端點：

您現在可以連接其中一個 psql：

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>後續步驟

- 閱讀適用於 PostgreSQL 的 Azure 資料庫超大規模的概念和操作指南，以將您的資料分散到多個于 postgresql 超大規模節點，並從適用於 PostgreSQL 的 Azure 資料庫超大規模的所有功能獲益。 :
    * [節點和資料表](../../postgresql/concepts-hyperscale-nodes.md)
    * [決定應用程式類型](../../postgresql/concepts-hyperscale-app-type.md)
    * [選擇散發資料行](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [資料表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [散發和修改資料表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [設計多租使用者資料庫](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [設計即時分析儀表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 在上述檔中，略過登 **入 Azure 入口網站**的區段，& **建立適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) **。 在 Azure Arc 部署中執行其餘步驟。 這些區段僅適用于適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 提供作為 Azure 雲端中的 PaaS 服務，但檔的其他部分直接適用于您 Azure Arc 啟用的于 postgresql 超大規模。

- [擴增適用於 PostgreSQL 超大規模資料庫的 Azure 資料庫伺服器群組](scale-out-postgresql-hyperscale-server-group.md)
- [儲存體設定和 Kubernetes 儲存體概念](storage-configuration.md)
- [擴充永久性磁片區宣告](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 資源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)