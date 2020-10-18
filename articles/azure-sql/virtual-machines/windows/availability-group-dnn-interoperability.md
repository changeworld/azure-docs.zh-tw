---
title: 可用性群組與 DNN 接聽程式的功能互通性
description: '深入瞭解使用某些 SQL Server 功能時的其他考慮，以及分散式網路名稱 (DNN) 接聽程式，並在 Azure Vm 上 SQL Server 上 Always On 可用性群組。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 567e1696bb06b3237c30c45384b8049ff82b5848
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168807"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>AG 和 DNN 接聽程式的功能互通性 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

有一些相依于硬式編碼虛擬網路名稱 (VNN) 的 SQL Server 功能。 因此，使用分散式網路名稱 (DNN) 接聽程式與您的 Always On 可用性群組和 Azure Vm 上的 SQL Server，可能會有一些額外的考慮。 

本文詳細說明可用性群組 DNN 接聽程式的 SQL Server 功能和互通性。 


## <a name="client-drivers"></a>用戶端驅動程式

若為 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驅動程式，使用者必須在連接字串中明確指定 DNN 接聽程式名稱和埠做為伺服器名稱。 若要確保在容錯移轉時能快速連線，請在 `MultiSubnetFailover=True` SQL 用戶端支援連接字串時加入連接字串。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、 [sqlcmd](/sql/tools/sqlcmd-utility)、 [Azure Data Studio](/sql/azure-data-studio/what-is)和[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools)的使用者必須明確指定 DNN 接聽程式名稱和埠，以連接到接聽程式的連接字串中的伺服器名稱。 

目前不支援透過 SQL Server Management Studio (SSMS) GUI 建立 DNN 接聽程式。 


## <a name="availability-groups-and-fci"></a>可用性群組和 FCI

您可以使用容錯移轉叢集實例 (FCI) 做為其中一個複本，來設定 Always On 可用性群組。 若要讓此設定與 DNN 接聽程式搭配使用， [容錯移轉叢集實例也必須使用 DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) ，因為沒有任何方法可將 FCI 虛擬 IP 位址放在 AG DNN IP 清單中。 

在此設定中，FCI 複本的鏡像端點 URL 必須使用 FCI DNN。 同樣地，如果使用 FCI 做為唯讀複本，則 FCI 複本的唯讀路由必須使用 FCI DNN。 

鏡像端點的格式為： `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` 。 

例如，如果您的 FCI DNN DNS 名稱是 `dnnlsnr` ，而 `5022` 是 FCI 的鏡像端點的埠，則用來建立端點 URL 的 Transact-sql (t-sql) 程式碼片段看起來像這樣： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同樣地，唯讀路由 URL 的格式為： `TCP://<FCI DNN DNS name>:<SQL Server instance port>` 。 

例如，如果您的 DNN DNS 名稱為 `dnnlsnr` ，而且 `1444` 是唯讀目標 SQL Server FCI 所使用的埠，則用來建立唯讀路由 URL 的 t-sql 程式碼片段如下所示： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

您可以省略 URL 中的埠（如果它是預設的1433埠）。 若為已命名的實例，請為指定的實例設定靜態埠，並在唯讀路由 URL 中指定它。  

## <a name="distributed-availability-group"></a>分散式可用性群組

DNN 接聽程式目前不支援分散式可用性群組。 

## <a name="replication"></a>複寫

交易式、合併和快照式複寫全都支援將 VNN 接聽程式取代為連接到接聽程式之複寫物件中的 DNN 接聽程式和埠。 

如需有關如何搭配使用複寫與可用性群組的詳細資訊，請參閱 [發行者和 ag](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)、 [訂閱者和 ag](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)，以及散發者 [和 ag](/sql/relational-databases/replication/configure-distribution-availability-group)。

## <a name="msdtc"></a>MSDTC

本機和叢集 MSDTC 都受到支援，但 MSDTC 會使用動態埠，這需要標準 Azure Load Balancer 來設定 HA 埠。 如此一來，VM 必須使用標準 IP 保留，否則無法對網際網路公開。 

定義兩個規則，一個用於 RPC 端點對應程式埠135，另一個用於實際的 MSDTC 埠。 在容錯移轉之後，在新的節點上變更 LB 規則之後，將它修改為新的 MSDTC 埠。 

如果 MSDTC 位於本機，請務必允許輸出通訊。 

## <a name="distributed-query"></a>分散式查詢 

分散式查詢依賴連結的伺服器，您可以使用 AG DNN 接聽程式和埠來設定它。 如果埠不是1433，請在設定連結的伺服器時，在 SQL Server Management Studio (SSMS) 中選擇 [ **使用其他資料來源** ] 選項。 

## <a name="filestream"></a>FileStream

Filestream 受支援，但不適用於使用者使用 Windows 檔案 API 存取範圍檔案共用的情況。 

## <a name="filetable"></a>Filetable

支援 Filetable，但不適用於使用者使用 Windows 檔案 API 存取範圍檔案共用的情況。 

## <a name="linked-servers"></a>連結的伺服器

使用 AG DNN 接聽程式名稱和埠來設定連結的伺服器。 如果埠不是1433，請在設定連結的伺服器時，在 SQL Server Management Studio (SSMS) 中選擇 [ **使用其他資料來源** ] 選項。 


## <a name="frequently-asked-questions"></a>常見問題集


- 哪個 SQL Server 版本提供 AG DNN 接聽程式支援？ 

   SQL Server 2019 CU8 和更新版本。

- 使用 DNN 接聽程式時，預期的容錯移轉時間為何？

   針對 DNN 接聽程式，容錯移轉時間將會是 AG 容錯移轉時間，而不會有任何額外的時間 (像是使用 Azure Load Balancer) 時的探查時間。

- SQL 用戶端是否有任何版本需求可支援使用 OLEDB 和 ODBC 來 DNN？

   我們建議 `MultiSubnetFailover=True` DNN 接聽程式的連接字串支援。 從 SQL Server 2012 (11. x) 開始提供。

- 使用 DNN 接聽程式時，是否需要變更任何 SQL Server 設定？ 

   SQL Server 不需要任何設定變更即可使用 DNN，但某些 SQL Server 功能可能需要更多考慮。 

- DNN 是否支援多重子網叢集？

   是。 無論子網為何，叢集都會將 DNS 中的 DNN 系結至可用性內所有複本的實體 IP 位址。 無論子網為何，SQL 用戶端都會嘗試 DNS 名稱的所有 IP 位址。 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always on 可用性群組](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

