---
title: 適用于 SAP 解決方案提供者的 Azure 監視器 |Microsoft Docs
description: 本文提供適用于 SAP 解決方案提供者的 Azure 監視器常見問題的解答。
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: f5df8bccc10ca64ee9a04f195299c5228b7274c1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356445"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>適用于 SAP 解決方案提供者的 Azure 監視器 (預覽) 

## <a name="overview"></a>概觀  

在 SAP 解決方案的 Azure 監視器內容中， *提供者類型* 是指特定的 *提供者* 。 例如 *SAP Hana* ，會針對 SAP 環境內的特定元件（例如 SAP Hana 資料庫）進行設定。 提供者包含對應元件的連接資訊，有助於從該元件收集遙測資料。 您可以使用多個提供者類型的多個提供者，或多個提供者類型的多個提供者，來設定 SAP 解決方案資源 (的一個 Azure 監視器也稱為 SAP 監視器資源) 。
   
客戶可以選擇設定不同的提供者類型，以在其 SAP 環境中從對應的元件啟用資料收集。 例如，客戶可以為 SAP Hana 提供者類型、另一個提供者提供高可用性叢集提供者類型的提供者，依此類推。  

客戶也可以選擇設定特定提供者類型的多個提供者，以重複使用相同的 SAP 監視器資源和相關聯的受控群組。 深入瞭解受控資源群組。 若為公開預覽，則支援下列提供者類型：   
- SAP HANA
- 高可用性叢集
- Microsoft SQL Server

![適用于 SAP 解決方案提供者的 Azure 監視器](./media/azure-monitor-sap/azure-monitor-providers.png)

建議客戶在部署 SAP 監視器資源時，從可用的提供者類型設定至少一個提供者。 藉由設定提供者，客戶可從已設定提供者的對應元件起始資料收集。   

如果客戶未在部署 SAP 監視器資源時設定任何提供者，雖然 SAP 監視器資源將成功部署，但不會收集任何遙測資料。 客戶可選擇在透過 Azure 入口網站中的 SAP 監視器資源部署之後新增提供者。 客戶隨時都可以從 SAP 監視器資源新增或刪除提供者。

> [!Tip]
> 如果您想要 Microsoft 執行特定的提供者，請在本檔結尾的連結中留下意見反應，或與您的帳戶小組聯繫。  

## <a name="provider-type-sap-hana"></a>提供者類型 SAP Hana

客戶可以設定提供者類型 *SAP Hana* 的一或多個提供者，以便從 SAP Hana 資料庫啟用資料收集。 SAP Hana 提供者會透過 SQL 埠連接到 SAP Hana 資料庫、從資料庫提取遙測資料，並將其推送至客戶訂用帳戶中的 Log Analytics 工作區。 SAP Hana 提供者會每隔1分鐘從 SAP Hana 資料庫收集資料。  

在公開預覽中，客戶可預期會看到下列資料與 SAP Hana 提供者：基礎結構使用率、SAP Hana 主機狀態、SAP Hana 系統複寫，以及 SAP Hana 備份遙測資料。 若要設定 SAP Hana 提供者、主機 IP 位址、HANA SQL 埠號碼，以及 SYSTEMDB 使用者名稱和密碼都是必要的。 建議客戶針對 SYSTEMDB 設定 SAP Hana 提供者，不過可以針對其他資料庫租使用者設定其他提供者。

![適用于 SAP 解決方案提供者的 Azure 監視器-SAP Hana](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>提供者類型高可用性叢集
客戶可以設定提供者類型 *高可用性* 叢集的一或多個提供者，以在 SAP 環境內從 Pacemaker 叢集啟用資料收集。 高可用性叢集提供者會使用 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 端點來連線到 Pacemaker、從資料庫提取遙測資料，並將其推送至客戶訂用帳戶中的 Log Analytics 工作區。 高可用性叢集提供者會每隔60秒從 Pacemaker 收集資料。  

在公開預覽中，客戶可預期會看到下列資料與高可用性叢集提供者：   
 - 以節點和資源狀態匯總顯示的叢集狀態 
 - [別人](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![適用于 SAP 解決方案提供者的 Azure 監視器-高可用性叢集](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

若要設定高可用性叢集提供者，涉及兩個主要步驟：

1. 在 Pacemaker 叢集中的 *每個* 節點上安裝 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 。

   您有兩個選項可安裝 ha_cluster_exporter：
   
   - 使用 Azure 自動化腳本來部署高可用性叢集。 腳本會在每個叢集節點上安裝 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 。  
   - 進行 [手動安裝](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build)。 

2. 為 Pacemaker 叢集中的 *每個* 節點設定高可用性叢集提供者。

   若要設定高可用性叢集提供者，需要下列資訊：
   
   - **名稱** 。 此提供者的名稱。 對於此 Azure 監視器的 SAP 解決方案實例而言，它應該是唯一的。
   - **Prometheus 端點** 。 通常是 HTTP \: // \<servername or ip address\> ： 9664/計量。
   - **SID** 。 針對 SAP 系統，請使用 SAP SID。 針對其他系統 (例如，NFS 叢集) ，請使用三個字元的叢集名稱。 SID 必須與受監視的其他群集不同。   
   - 叢集 **名稱** 。 建立叢集時所使用的叢集名稱。 叢集名稱可以在 cluster 屬性中找到 `cluster-name` 。
   - **主機名稱** 。 VM 的 Linux 主機名稱。  

## <a name="provider-type-microsoft-sql-server"></a>提供者類型 Microsoft SQL server

客戶可以設定提供者類型 *Microsoft SQL Server* 的一或多個提供者，以便從 [虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)啟用資料收集。 SQL Server 提供者會透過 SQL 埠連線到 Microsoft SQL Server，從資料庫提取遙測資料，並將其推送至客戶訂用帳戶中的 Log Analytics 工作區。 必須將 SQL Server 設定為使用 SQL 驗證和 SQL Server 登入，並以 SAP DB 作為提供者的預設資料庫，才能建立。 SQL Server 提供者會在每隔60秒（從 SQL Server 到每小時）收集資料。  

在公開預覽中，客戶可預期會看到下列資料與 SQL Server 提供者：基礎結構使用率、熱門 SQL 語句、最大的資料表、SQL Server 錯誤記錄檔中記錄的問題、封鎖進程和其他人。  

若要設定 Microsoft SQL Server 提供者，必須要有 SAP 系統識別碼、主機 IP 位址 SQL Server 埠號碼，以及 SQL Server 登入名稱和密碼。

![適用于 SAP 解決方案提供者的 Azure 監視器-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>後續步驟

- 為 SAP 解決方案資源建立您的第一個 Azure 監視器。
- 您有關于 SAP 解決方案 Azure 監視器的問題嗎？ 查看 [常見問題](./azure-monitor-faq.md) 區段
