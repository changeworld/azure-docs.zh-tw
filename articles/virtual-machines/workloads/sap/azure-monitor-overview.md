---
title: 適用于 SAP 解決方案的 Azure 監視器總覽和架構 |Microsoft Docs
description: 本文提供適用于 SAP 解決方案的 Azure 監視器常見問題的解答
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 1c33011d947d6dc9dd9ee4dd6331c24c06d99b38
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693819"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>適用于 SAP 解決方案的 Azure 監視器 (預覽) 

## <a name="overview"></a>概觀

適用于 SAP 解決方案的 Azure 監視器是 Azure 原生的監視產品，可供客戶在 Azure 上執行 SAP 環境。 此產品適用于 [Azure 上的 SAP 的虛擬機器](./hana-get-started.md) 和 [Azure 上的 SAP 大型實例](./hana-overview-architecture.md)。
有了適用于 SAP 解決方案的 Azure 監視器，客戶可以從一個集中位置的 Azure 基礎結構和資料庫收集遙測資料，並以視覺化方式將遙測資料相互關聯，以加快疑難排解的速度。

Azure 監視器可透過 Azure Marketplace 提供 SAP 解決方案。 它提供簡單、直覺的設定體驗，並只需按幾下滑鼠就能針對 SAP 解決方案的 Azure 監視器部署資源， (稱為 **sap 監視器資源**) 。

客戶可以藉由新增該元件的對應 **提供者** ，來監視 SAP 環境的不同元件，例如 Azure 虛擬機器、高可用性叢集、SAP Hana 資料庫等等。

支援的基礎結構：

- Azure 虛擬機器
- Azure 大型實例

支援的資料庫：
- SAP HANA 資料庫
- Microsoft SQL server

適用于 SAP 解決方案的 Azure 監視器會使用現有 [Azure 監視器](../../../azure-monitor/overview.md) 功能的強大功能，例如 Log Analytics 和活頁 [簿](../../../azure-monitor/platform/workbooks-overview.md) ，以提供更多監視功能。 客戶可以藉由編輯 Azure 監視器針對 SAP 解決方案提供的預設活頁簿、撰寫[自訂查詢](../../../azure-monitor/log-query/log-analytics-tutorial.md)，以及使用 Azure Log Analytics 工作區建立[自訂警示](../../../azure-monitor/learn/tutorial-response.md)，來建立[自訂視覺效果](../../../azure-monitor/platform/workbooks-overview.md#getting-started)、利用[彈性的保留期間](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)，以及將監視資料與票證系統連線。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Azure 監視器 SAP 解決方案收集哪些資料？

SAP 解決方案 Azure 監視器中的資料收集取決於客戶所設定的提供者。 在公開預覽期間，會收集下列資料。

高可用性 Pacemaker 叢集遙測：
- 節點、資源和 SBD 裝置狀態
- Pacemaker 位置條件約束
- 仲裁投票和響鈴狀態
- [其他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP Hana 遙測：
- CPU、記憶體、磁片和網路使用量
- HANA System Replication (HSR) 
- HANA 備份
- HANA 主機狀態
- 索引伺服器和名稱伺服器角色

Microsoft SQL server 遙測：
- CPU、記憶體、磁片使用量
- 主機名稱、SQL 實例名稱、SAP 系統識別碼
- 經過一段時間的批次要求、編譯和頁面的預期壽命
- 一段時間內的前10個最高成本 SQL 語句
- SAP 系統中的前12個最大資料表
- SQL Server 錯誤記錄檔中記錄的問題
- 在一段時間內封鎖進程和 SQL 等候統計資料

 (Linux) 的作業系統遙測 
- CPU 使用率、分支計數、執行中和已封鎖的進程。 
- 利用、快取、緩衝處理的記憶體使用量和散發。 
- 交換使用率、分頁和交換速率。 
- 檔案系統使用率、每個區塊裝置讀取和寫入的位元組數目。 
- 每個區塊裝置的讀取/寫入延遲。 
- 持續性的 i/o 計數、持續性記憶體讀取/寫入位元組。 
- 網路封包 in/out、Network bytes in/out 

## <a name="data-sharing-with-microsoft"></a>與 Microsoft 共用資料

Azure 監視器 SAP 解決方案會收集系統中繼資料，為我們的 Azure 上的 SAP 客戶提供改善的支援。 未收集 PII/EUII。
客戶可以在建立 SAP 解決方案資源的 Azure 監視器時，從下拉式清單中選擇 [ *共用* ]，以啟用與 Microsoft 的資料共用。
強烈建議客戶啟用資料共用，因為它可讓 Microsoft 支援和工程小組更詳細地瞭解客戶環境，並為要徑任務 Azure 上的 SAP 客戶提供改善的支援。

## <a name="architecture-overview"></a>架構概觀

在高階中，下圖說明 SAP 解決方案的 Azure 監視器如何從 SAP Hana 資料庫收集遙測資料。 無論 SAP Hana 是部署在 Azure 虛擬機器或 Azure 大型實例上，架構都是不受限制的。

![適用于 SAP 解決方案架構的 Azure 監視器](./media/azure-monitor-sap/azure-monitor-architecture.png)

架構的主要元件如下：
- Azure 入口網站–客戶的起點。 客戶可以在 Azure 入口網站中流覽至 marketplace，並探索 SAP 解決方案的 Azure 監視器
- 適用于 SAP 解決方案資源的 Azure 監視器-供客戶查看監視遙測的登陸位置
- 受控資源群組–會自動部署為 SAP 解決方案資源部署的 Azure 監視器一部分。 在受控資源群組內部署的資源有助於收集遙測。 部署的重要資源和其用途如下：
   - Azure 虛擬機器：也稱為 *收集器 VM*。 這是 Standard_B2ms VM。 此 VM 的主要用途是裝載 *監視* 承載。 監視承載指的是從來源系統收集遙測資料，並將收集到的資料傳輸至監視架構的邏輯。 在上圖中，監視承載包含透過 SQL 埠連接到 SAP Hana 資料庫的邏輯。
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md)：已部署此資源以安全地保存 SAP Hana 資料庫認證，以及儲存 [提供者](./azure-monitor-providers.md)的相關資訊。
   - Log Analytics 工作區：遙測資料所在的目的地。
      - 視覺效果是以使用 [Azure 活頁簿](../../../azure-monitor/platform/workbooks-overview.md)的 Log Analytics 中的遙測為基礎。 客戶可以自訂視覺效果。 客戶也可以將活頁簿中的活頁簿或特定視覺效果釘選到 Azure 儀表板，以取得自動重新整理功能，其資料細微性最短為30分鐘。
      - 客戶可以在部署時選擇此選項，以在與 SAP 監視器資源相同的訂用帳戶內使用其現有的工作區。
      - 客戶可以使用 Kusto 查詢語言 (KQL) ，對 Log Analytics 工作區中的原始資料表執行 [查詢](../../../azure-monitor/log-query/log-query-overview.md) 。 查看 *自訂記錄*。

> [!Note]
> 客戶須負責修補和維護 VM （部署在受控資源群組中）。

> [!Tip]
> 客戶可以選擇使用現有的 Log Analytics 工作區進行遙測收集，如果它部署在與資源相同的 Azure 訂用帳戶中，以用於 Azure 監視器的 SAP 解決方案。

### <a name="architecture-highlights"></a>架構重點

以下是架構的重要重點：
 - **多重實例** -客戶可以針對特定元件類型的多個實例建立監視器 (例如，HANA DB、HA 叢集、Microsoft SQL server) 在 VNET 內的多個 sap sid 中，並具有適用于 SAP 解決方案 Azure 監視器的單一資源。
 - **多個提供者** -上述架構圖顯示 SAP Hana 提供者作為範例。 同樣地，客戶可以為對應的元件設定更多提供者 (例如 HANA DB、HA 叢集、Microsoft SQL server) ，以從這些元件收集資料。
 - **開放原始** 碼-適用于 SAP 解決方案的 Azure 監視器原始程式碼可在 [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)中取得。 客戶可以參考提供者程式碼，並深入瞭解產品、投稿或分享意見反應。
 - 可延伸的 **查詢架構**-收集遙測資料的 SQL 查詢是以 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)撰寫的。 您可以輕鬆地新增更多可收集更多遙測資料的 SQL 查詢。 客戶可以透過本檔結尾的連結，或聯繫其帳戶小組，要求將特定遙測資料新增至 Azure 監視器的 SAP 解決方案。

## <a name="pricing"></a>定價
適用于 SAP 解決方案的 Azure 監視器是免費的產品 (不提供授權費用) 。 客戶須負責支付受控資源群組中基礎元件的成本。

## <a name="next-steps"></a>後續步驟

瞭解提供者，並建立您的第一個 SAP 解決方案資源 Azure 監視器。
 - 深入瞭解 [提供者](./azure-monitor-providers.md)
 - [使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions](azure-monitor-sap-quickstart-powershell.md)
 - 您有關于 SAP 解決方案 Azure 監視器的問題嗎？ 查看 [常見問題](./azure-monitor-faq.md) 區段
