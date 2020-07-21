---
title: SAP 解決方案的 Azure 監視器總覽和架構 |Microsoft Docs
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
ms.openlocfilehash: 306983e612793eb92dd95fe57e7177da17874893
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525427"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>適用于 SAP 解決方案的 Azure 監視器（預覽）

## <a name="overview"></a>概觀  

適用于 SAP 解決方案的 Azure 監視器是適用于客戶的 Azure 原生監視產品，其在 Azure 上執行其 SAP 環境。 此產品同時適用于[Azure 上的 SAP 虛擬機器](./hana-get-started.md)和[Azure 上的 SAP 大型實例](./hana-overview-architecture.md)。  
透過適用于 SAP 解決方案的 Azure 監視器，客戶可以在一個集中位置收集 Azure 基礎結構和資料庫的遙測資料，並以視覺化方式將遙測資料相互關聯，以進行更快速的疑難排解。  

SAP 解決方案的 Azure 監視器是透過 Azure Marketplace 提供。 它提供簡單、直覺的安裝體驗，並只需按幾下滑鼠，就能針對 SAP 解決方案（稱為**sap 監視器資源**）的 Azure 監視器部署資源。  

客戶可以藉由為該元件新增對應的**提供者**，來監視 SAP 環境的不同元件，例如 Azure 虛擬機器、高可用性叢集、SAP Hana 資料庫等等。 

支援的基礎結構：  

- Azure 虛擬機器  
- Azure 大型實例  

支援的資料庫：  
- SAP HANA 資料庫 
- Microsoft SQL server  

SAP 解決方案的 Azure 監視器利用現有[Azure 監視器](../../../azure-monitor/overview.md)功能（例如 Log Analytics 和活頁[簿](../../../azure-monitor/platform/workbooks-overview.md)）的威力來提供其他監視功能。 客戶可以藉由編輯 SAP 解決方案 Azure 監視器所提供的預設活頁簿來建立[自訂視覺效果](../../../azure-monitor/platform/workbooks-overview.md#getting-started)、撰寫[自訂查詢](../../../azure-monitor/log-query/get-started-portal.md)，以及使用 Azure Log Analytics 工作區建立[自訂警示](../../../azure-monitor/learn/tutorial-response.md)、利用[彈性的保留期間](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)，以及將監視資料與票證系統連線。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Azure 監視器 SAP 解決方案收集哪些資料？

SAP 解決方案 Azure 監視器中的資料收集取決於客戶所設定的提供者。 在公開預覽期間，系統會收集下列資料。  

高可用性 Pacemaker 叢集遙測：  
- 節點、資源和 SBD 裝置狀態  
- Pacemaker 位置條件約束  
- 仲裁投票和響鈴狀態  
- [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)
  
SAP Hana 遙測：  
- CPU、記憶體、磁片和網路使用率  
- HANA 系統複寫（HSR）  
- HANA 備份  
- HANA 主機狀態  
- 索引伺服器和名稱伺服器角色  

Microsoft SQL server 遙測：  
- CPU、記憶體、磁片使用量  
- 主機名稱、SQL 實例名稱、SAP 系統識別碼  
- 一段時間內的批次要求、編譯及分頁生命週期  
- 一段時間內前10大成本最高的 SQL 語句  
- SAP 系統中前12個最大的資料表  
- SQL Server 錯誤記錄檔中記錄的問題  
- 一段時間內封鎖進程和 SQL 等候統計資料  

## <a name="data-sharing-with-microsoft"></a>與 Microsoft 共用資料

Azure 監視器的 SAP 解決方案會收集系統中繼資料，為我們的 Azure 上的 SAP 客戶提供更好的支援。 不會收集 PII/EUII。  
客戶可以在建立 SAP 解決方案資源的 Azure 監視器時，從下拉式選單選擇 [*共用*]，以啟用與 Microsoft 共用的資料。  
強烈建議客戶啟用資料共用，因為它可讓 Microsoft 支援服務和工程小組取得客戶環境的詳細資訊，並為要徑任務 Azure 上的 SAP 客戶提供更好的支援。  

## <a name="architecture-overview"></a>架構概觀

在高階層級中，下圖說明 SAP 解決方案的 Azure 監視器如何從 SAP Hana 資料庫收集遙測。 無論 SAP Hana 是部署在 Azure 虛擬機器或 Azure 大型實例上，架構都是不可知的。

![SAP 解決方案架構的 Azure 監視器](./media/azure-monitor-sap/azure-monitor-architecture.png)

架構的主要元件如下：   
- Azure 入口網站–客戶的起點。 客戶可以在 Azure 入口網站內流覽至 marketplace，並探索 SAP 解決方案的 Azure 監視器
- 適用于 SAP 解決方案資源的 Azure 監視器–客戶用來觀看監視遙測資料的登陸位置 
- 受控資源群組-會自動部署為 SAP 解決方案資源部署 Azure 監視器的一部分。 在受管理資源群組中部署的資源可協助收集遙測。 已部署的重要資源及其目的如下：  
   - Azure 虛擬機器：也稱為*收集器 VM*。 這是 Standard_B2ms 的 VM。 此 VM 的主要目的是要裝載*監視*承載。 監視裝載是指從來源系統收集遙測，並將收集到的資料傳輸到監視架構的邏輯。 在上圖中，監視承載包含透過 SQL 埠連接到 SAP Hana 資料庫的邏輯。
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md)：部署此資源以安全地保存 SAP Hana 的資料庫認證，並儲存[提供者](./azure-monitor-providers.md)的相關資訊。  
   - Log Analytics 工作區：遙測資料所在的目的地。  
      - 視覺效果是以使用[Azure 活頁簿](../../../azure-monitor/platform/workbooks-overview.md)的 Log Analytics 中的遙測為基礎。 客戶可以自訂視覺效果。 客戶也可以將活頁簿中的活頁簿或特定視覺效果釘選到 Azure 儀表板，以取得 autorefresh 功能，最小的30分鐘資料細微性。  
      - 客戶可以在部署時選擇此選項，以在與 SAP 監視器資源相同的訂用帳戶內使用其現有的工作區。 
      - 客戶可以使用 Kusto 查詢語言（KQL），對 Log Analytics 工作區中的原始資料表執行[查詢](../../../azure-monitor/log-query/log-query-overview.md)。 查看*自訂記錄*檔。  

> [!Note]
> 客戶須負責修補和維護部署在受控資源群組中的 VM。  

> [!Tip]
> 如果您將現有的 Log Analytics 工作區部署在與 SAP 解決方案 Azure 監視器的資源相同的 Azure 訂用帳戶中，則可以選擇使用適用于遙測集合的現有記錄分析工作區。

### <a name="architecture-highlights"></a>架構重點

以下是此架構的重要重點：
 - **多重實例**-客戶可以使用 sap 解決方案的單一 Azure 監視器資源，針對 VNET 內的多個 sap sid，建立指定元件類型（例如，HANA DB、HA 叢集、Microsoft SQL server）之多個實例的監視。 
 - **多提供者**-上述架構圖顯示 SAP Hana 提供者做為範例。 同樣地，客戶也可以為對應的元件（例如，HANA DB、HA 叢集、Microsoft SQL server）設定其他提供者，以收集來自這些元件的資料。
 - **開放原始**碼-適用于 SAP 解決方案 Azure 監視器的原始程式碼可在[GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)中取得。 客戶可以參考提供者程式碼，並深入瞭解產品、參與或分享意見反應。 
 - 可延伸的**查詢架構**-收集遙測資料的 SQL 查詢會以[JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)撰寫。 您可以輕鬆地新增額外的 SQL 查詢來收集更多遙測資料。 客戶可以透過本檔結尾的連結取得意見反應，或聯繫其帳戶小組，要求將特定遙測資料新增至 Azure 監視器的 SAP 解決方案。

## <a name="pricing"></a>定價
適用于 SAP 解決方案的 Azure 監視器是免費產品（無授權費用）。 客戶須負責支付受控資源群組中基礎元件的成本。

## <a name="next-steps"></a>後續步驟

瞭解提供者，並建立您的第一個 Azure 監視器的 SAP 解決方案資源。
 - 深入瞭解[提供者](./azure-monitor-providers.md)
 - 您對 SAP 解決方案的 Azure 監視器有任何疑問嗎？ 查看[常見問題](./azure-monitor-faq.md)一節
