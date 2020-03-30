---
title: 有關 Azure 網路觀察程式的常見問題 （FAQ） |微軟文檔
description: 本文回答了有關 Azure 網路觀察程式服務的常見問題。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471851"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>有關 Azure 網路觀察程式的常見問題 （FAQ）
[Azure 網路觀察程式](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)服務提供了一套工具，用於監視、診斷、查看指標以及啟用或禁用 Azure 虛擬網路中資源的日誌。 本文回答了有關服務的常見問題。

## <a name="general"></a>一般

### <a name="what-is-network-watcher"></a>什麼是網路監看員？
網路觀察程式旨在監視和修復 IaaS（基礎架構即服務）元件的網路運行狀況，這些元件包括 Azure 虛擬網路中的虛擬機器、虛擬機器、應用程式閘道、負載等化器和其他資源。 它不是用於監視 PaaS（平臺即服務）基礎架構或獲取 Web/移動分析的解決方案。

### <a name="what-tools-does-network-watcher-provide"></a>網路觀察程式提供哪些工具？
網路觀察程式提供三組主要功能
* 監視
  * [拓撲視圖](https://docs.microsoft.com/azure/network-watcher/view-network-topology)顯示虛擬網路中的資源及其之間的關係。
  * [連接監視器](https://docs.microsoft.com/azure/network-watcher/connection-monitor)允許您監視 VM 與另一個網路資源之間的連接和延遲。
  * [網路效能監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor)允許您跨混合網路架構、快速路由電路和服務/應用程式端點監控連接和延遲。  
* 診斷
  * [IP 流驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)允許您在 VM 級別檢測流量篩選問題。
  * [下一跳](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)可説明您驗證流量路由並檢測路由問題。
  * [連接故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal)支援 VM 與另一個網路資源之間的一次性連接和延遲檢查。
  * [資料包捕獲](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)使您能夠捕獲虛擬網路中 VM 上的所有流量。
  * [VPN 疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview)在 VPN 閘道和連接上運行多個診斷檢查，以説明調試問題。
* 記錄
  * [NSG 流日誌](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)允許您記錄[網路安全性群組 （NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)中的所有流量
  * [流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)處理 NSG 流日誌資料，使您能夠視覺化、查詢、分析和瞭解網路流量。


有關詳細資訊，請參閱[網路觀察程式概述頁](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)。


### <a name="how-does-network-watcher-pricing-work"></a>網路觀察程式定價如何工作？
訪問網路觀察元件及其[定價的定價頁面](https://azure.microsoft.com/pricing/details/network-watcher/)。

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>網路觀察程式支援/可在哪些區域？
您可以在[Azure 服務可用性頁上](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)查看最新的區域可用性

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>使用網路觀察程式需要哪些許可權？
請參閱[使用網路觀察程式所需的 RBAC 許可權](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions)清單。 對於部署資源，您需要向網路觀察者 RG 提供參與者許可權（見下文）。

### <a name="how-do-i-enable-network-watcher"></a>如何啟用網路監看員？
每個訂閱[將自動啟用](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/)網路觀察程式服務。

### <a name="what-is-the-network-watcher-deployment-model"></a>什麼是網路觀察程式部署模型？
網路觀察程式父資源在每個區域中使用唯一實例部署。 命名格式：NetworkWatcher_RegionName。 示例：NetworkWatcher_centralus是"美國中部"區域的網路觀察器資源。

### <a name="what-is-the-networkwatcherrg"></a>什麼是網路觀察家RG？
網路觀察程式資源位於自動創建的隱藏**網路觀察器資源**組中。 例如，NSG 流日誌資源是網路觀察程式的子資源，並在網路觀察器RG中啟用。

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>為什麼我需要安裝網路觀察程式擴展？ 
需要從 VM 生成或攔截流量的任何功能都需要網路觀察程式擴展。 

### <a name="which-features-require-the-network-watcher-extension"></a>哪些功能需要網路觀察程式擴展？
資料包捕獲、連接故障排除和連接監視器功能需要存在網路觀察程式擴展。

### <a name="what-are-resource-limits-on-network-watcher"></a>網路觀察程式的資源限制是什麼？
有關所有限制，請參閱[服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits)頁面。  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>為什麼每個區域只允許一個網路觀察程式實例？ 
網路觀察程式只需啟用一次訂閱才能使其功能正常工作，這不是服務限制。

### <a name="how-can-i-manage-the-network-watcher-resource"></a>如何管理網路觀察程式資源？ 
網路觀察程式資源表示網路觀察程式的後端服務，由 Azure 完全管理。 客戶無需對其進行管理。 資源上不支援移動等操作。 但是[，可以刪除資源](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal)。 

## <a name="nsg-flow-logs"></a>NSG 流日誌

### <a name="what-does-nsg-flow-logs-do"></a>NSG 流日誌的作用是什麼？
Azure 網路資源可以通過[網路安全性群組 （NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)進行組合和管理。 NSG 流日誌使您能夠記錄有關通過 NSG 的所有流量的 5 個元流量資訊。 原始流日誌將寫入 Azure 存儲帳戶，從該帳戶可以根據需要進一步處理、分析、查詢或匯出它們。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>如何使用帶有防火牆後面的存儲帳戶的 NSG 流日誌？

要使用防火牆後面的存儲帳戶，必須為受信任的 Microsoft 服務提供訪問存儲帳戶的例外：

* 通過在門戶上的全域搜索中或從["存儲帳戶"頁上](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)鍵入存儲帳戶的名稱，導航到存儲帳戶
* 在 [設定]**** 區段下，選取 [防火牆和虛擬網路]****
* 在"允許從"中，選擇 **"選定網路**"。 然後在 **"例外**"下，勾選 **"允許受信任的 Microsoft 服務訪問此存儲帳戶"** 旁邊的核取方塊 
* 如果早已選取，則不需要再變更。  
* 在[NSG 流日誌概述頁面上](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)找到目標 NSG，並在選中上述存儲帳戶後啟用 NSG 流日誌。

您可以在幾分鐘後檢查儲存體記錄，屆時應該就會看到已更新的時間戳記或新建立的 JSON 檔案。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>如何使用與服務終結點後面的存儲帳戶一起使用的 NSG 流日誌？

NSG 流日誌與服務終結點相容，無需任何額外配置。 請參閱有關在虛擬網路中[啟用服務終結點的教程](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint)。


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>流日誌版本 1 & 2 之間的區別是什麼？
Flow Logs 版本 2 引入了*流狀態*的概念&存儲有關傳輸的位元組和資料包的資訊。 [閱讀更多](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)。

## <a name="next-steps"></a>後續步驟
 - 前往我們的[文檔概述頁面](https://docs.microsoft.com/azure/network-watcher/)獲取一些教程，説明您開始使用網路觀察程式。
