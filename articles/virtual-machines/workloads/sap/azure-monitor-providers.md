---
title: SAP 解決方案提供者的 Azure 監視器 |Microsoft Docs
description: 本文提供適用于 SAP 解決方案的 Azure 監視器常見問題的解答
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: a7e44046de3eccab83e8315e6adea150a146e660
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964187"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>適用于 SAP 解決方案提供者的 Azure 監視器（預覽）

## <a name="overview"></a>總覽  

在 SAP 解決方案的 Azure 監視器內容中，*提供者類型*是指特定的*提供者*。 例如*SAP Hana*，它是針對 SAP 環境內的特定元件所設定，如 SAP Hana 資料庫。 提供者包含對應元件的連接資訊，並有助於從該元件收集遙測資料。 您可以使用相同提供者類型的多個提供者，或多個提供者類型的多個提供者，設定 SAP 解決方案資源的一個 Azure 監視器（也稱為 SAP 監視器資源）。
   
客戶可以選擇設定不同的提供者類型，以在其 SAP 環境中從對應的元件啟用資料收集。 例如，客戶可以為 SAP Hana 提供者類型設定一個提供者，另一個提供者用於高可用性叢集提供者類型等等。  

客戶也可以選擇設定特定提供者類型的多個提供者，以重複使用相同的 SAP 監視器資源和相關聯的受控群組。 深入瞭解受控資源群組。 若為公開預覽，則支援下列提供者類型：   
- SAP HANA
- 高可用性叢集
- Microsoft SQL Server

![SAP 解決方案提供者的 Azure 監視器](./media/azure-monitor-sap/azure-monitor-providers.png)

建議客戶在部署 SAP 監視器資源時，從可用的提供者類型設定至少一個提供者。 藉由設定提供者，客戶可以從已設定提供者的對應元件起始資料收集。   

如果客戶未在部署 SAP 監視器資源時設定任何提供者，雖然 SAP 監視器資源將會成功部署，但不會收集遙測資料。 客戶可以選擇透過 Azure 入口網站內的 SAP 監視器資源，在部署之後新增提供者。 客戶可以隨時在 SAP 監視器資源中新增或刪除提供者。

> [!Tip]
> 如果您想要 Microsoft 執行特定的提供者，請透過本檔結尾的連結留下意見反應，或與您的帳戶小組聯繫。  

## <a name="provider-type-sap-hana"></a>提供者類型 SAP Hana

客戶可以設定一或多個提供者類型*SAP Hana* ，以啟用從 SAP Hana 資料庫收集資料。 SAP Hana 提供者會透過 SQL 埠連接到 SAP Hana 資料庫、從資料庫提取遙測資料，並將其推送至客戶訂用帳戶中的 Log Analytics 工作區。 SAP Hana 提供者會從 SAP Hana 資料庫每隔1分鐘收集資料一次。  

在公開預覽中，客戶可以預期使用 SAP Hana 提供者來查看下列資料：基礎結構使用率、SAP Hana 主機狀態、SAP Hana 系統複寫，以及 SAP Hana 備份遙測資料。 若要設定 SAP Hana 提供者，必須要有主機 IP 位址、HANA SQL 埠號碼和 SYSTEMDB 使用者名稱和密碼。 建議客戶針對 SYSTEMDB 設定 SAP Hana 提供者，但其他的提供者可以針對其他資料庫租使用者進行設定。

![SAP 解決方案提供者的 Azure 監視器-SAP Hana](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>提供者類型高可用性叢集
客戶可以設定一或多個提供者類型的*高可用性*叢集，以便在 SAP 環境內從 Pacemaker 叢集收集資料。 高可用性叢集提供者會使用[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)端點來連線至 Pacemaker，從資料庫提取遙測資料，並將其推送至客戶訂用帳戶中的 Log Analytics 工作區。 高可用性叢集提供者會每60秒從 Pacemaker 收集資料。  

在公開預覽中，客戶可以預期會看到具有高可用性叢集提供者的下列資料：   
 - 以節點和資源狀態匯總顯示的叢集狀態 
 - [那裡](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![SAP 解決方案提供者的 Azure 監視器-高可用性叢集](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

若要設定高可用性叢集提供者，涉及兩個主要步驟： 
1. 在 Pacemaker 叢集中的*每個*節點上安裝[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 
    - 客戶可以使用 Azure 自動化腳本來部署高可用性叢集。 腳本將會在每個叢集節點上安裝[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 。  
    - 或客戶可以執行手動安裝，請遵循[此頁面](https://github.com/ClusterLabs/ha_cluster_exporter)上的步驟 
2. 在 Pacemaker 叢集中的*每個*節點設定高可用性叢集提供者  
  若要設定高可用性叢集提供者，需要 Prometheus URL、叢集名稱、主機名稱和系統識別碼。   
  建議客戶為每個叢集節點設定一個提供者。   

## <a name="provider-type-microsoft-sql-server"></a>提供者類型 Microsoft SQL server

客戶可以設定提供者類型的一或多個提供者*Microsoft SQL Server* ，以從[虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)啟用資料收集。 SQL Server 提供者會透過 SQL 埠連接到 Microsoft SQL Server、從資料庫提取遙測資料，並將其推送至客戶訂用帳戶中的 Log Analytics 工作區。 必須針對 SQL 驗證設定 SQL Server，並使用 SAP DB 作為提供者的預設資料庫，才能建立 SQL Server 登入。 SQL Server 提供者會收集 SQL Server 每隔60秒的資料（每小時）。  

在公開預覽中，客戶可以預期使用 SQL Server 提供者看到下列資料：基礎結構使用率、熱門 SQL 語句、最大的資料表、SQL Server 錯誤記錄檔中記錄的問題、封鎖進程和其他專案。  

若要設定 Microsoft SQL Server 提供者，必須要有 SAP 系統識別碼、主機 IP 位址 SQL Server 埠號碼，以及 SQL Server 登入名稱和密碼。

![SAP 解決方案提供者的 Azure 監視器-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>後續步驟

- 建立您的第一個 Azure 監視器的 SAP 解決方案資源。
- 您對 SAP 解決方案的 Azure 監視器有任何疑問嗎？ 查看[常見問題](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/azure-monitor-faq)一節
