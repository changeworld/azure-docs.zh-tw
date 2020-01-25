---
title: 關於 Azure 網路監看員的常見問題（FAQ） |Microsoft Docs
description: 本文會回答有關 Azure 網路監看員服務的常見問題。
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
ms.openlocfilehash: 1cc3664ff8472a6b5a73fa89588611f59ac27e6a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720261"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>關於 Azure 網路監看員的常見問題（FAQ）
[Azure 網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)服務提供一套工具，可用來監視、診斷、查看計量，以及啟用或停用 Azure 虛擬網路中的資源記錄。 本文會回答有關此服務的常見問題。

## <a name="general"></a>一般

### <a name="what-is-network-watcher"></a>什麼是網路監看員？
網路監看員的設計是用來監視和修復 IaaS （基礎結構即服務）元件的網路健全狀況，其中包括虛擬機器、虛擬網路、應用程式閘道、負載平衡器，以及 Azure 虛擬網路中的其他資源。 它不是用來監視 PaaS （平臺即服務）基礎結構或取得 web/行動分析的解決方案。

### <a name="what-tools-does-network-watcher-provide"></a>網路監看員提供哪些工具？
網路監看員提供三組主要的功能
* 監視
  * [[拓撲] 視圖](https://docs.microsoft.com/azure/network-watcher/view-network-topology)會顯示虛擬網路中的資源，以及它們之間的關聯性。
  * [連接監視器](https://docs.microsoft.com/azure/network-watcher/connection-monitor)可讓您監視 VM 與另一個網路資源之間的連線和延遲。
  * [網路效能監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor)可讓您監視混合式網路架構、Expressroute 線路和服務/應用程式端點之間的連線能力和延遲。  
* 診斷程式
  * [IP 流量驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)可讓您偵測 VM 層級的流量篩選問題。
  * [下一個躍點](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)可協助您驗證流量路由和偵測路由問題。
  * 連線[疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal)可讓 VM 與另一個網路資源之間進行一次性的連線和延遲檢查。
  * 封[包捕獲](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)可讓您在虛擬網路中的 VM 上，捕捉所有流量。
  * [Vpn 疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview)會針對您的 VPN 閘道和連線執行多個診斷檢查，以協助進行偵錯工具的問題。
* 記錄
  * [NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)可讓您記錄[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)中的所有流量
  * 使用[分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)會處理您的 NSG 流量記錄資料，讓您可以視覺化、查詢、分析及瞭解您的網路流量。


如需詳細資訊，請參閱[網路監看員總覽頁面](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)。


### <a name="how-does-network-watcher-pricing-work"></a>網路監看員定價如何運作？
請流覽[定價頁面](https://azure.microsoft.com/pricing/details/network-watcher/)以取得網路監看員元件和其定價。

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>網路監看員支援/提供哪些區域？
您可以在[Azure 服務可用性頁面](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)上查看最新的區域可用性

### <a name="what-are-resource-limits-on-network-watcher"></a>網路監看員上的資源限制為何？
如需所有限制，請參閱[服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits)頁面。  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>為什麼每個區域只允許一個網路監看員實例？
網路監看員只需要針對訂用帳戶啟用一次，它的功能才能正常執行，這不是服務的限制。

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>為什麼需要安裝網路監看員延伸模組？ 
需要產生或攔截 VM 流量的任何功能都需要網路監看員延伸模組。 

### <a name="which-features-require-the-network-watcher-extension"></a>哪些功能需要網路監看員延伸模組？
只有封包捕獲、連線疑難排解和連線監視需要有網路監看員延伸模組。

## <a name="nsg-flow-logs"></a>NSG 流量記錄

### <a name="what-does-nsg-flow-logs-do"></a>NSG 流量記錄有哪些功能？
您可以透過[網路安全性群組（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)結合和管理 Azure 網路資源。 NSG 流量記錄可讓您透過 Nsg 記錄所有流量的5個元組流量資訊。 未經處理的流量記錄會寫入 Azure 儲存體帳戶，讓您可以視需要進一步進行處理、分析、查詢或匯出。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>如何? 將 NSG 流量記錄與防火牆後方的儲存體帳戶搭配使用？

若要使用防火牆後方的儲存體帳戶，您必須為受信任的 Microsoft 服務提供例外狀況，才能存取您的儲存體帳戶：

* 在入口網站上的全域搜尋中，或從 [[儲存體帳戶] 頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)輸入儲存體帳戶的名稱，以流覽至儲存體帳戶。
* 在 [設定] 區段下，選取 [防火牆和虛擬網路]
* 在 [允許存取來源] 中，選取 [**選取的網路**]。 然後在 [**例外**狀況] 底下，勾選 [**允許受信任的 Microsoft 服務存取此儲存體帳戶**] 旁的方塊 
* 如果早已選取，則不需要再變更。  
* 在 [ [NSG 流量記錄] 總覽頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)上找出您的目標 NSG，並啟用已選取上述儲存體帳戶的 NSG 流量記錄。

您可以在幾分鐘後檢查儲存體記錄，屆時應該就會看到已更新的時間戳記或新建立的 JSON 檔案。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>如何? 在服務端點後方的儲存體帳戶中使用 NSG 流量記錄嗎？

NSG 流量記錄會與服務端點 compantible，而不需要任何額外的設定。 請參閱在虛擬網路中[啟用服務端點的教學](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint)課程。


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>流量記錄版本 1 & 2 之間的差異為何？
流量記錄第2版引進了*流程狀態*的概念，& 儲存傳輸位元組和封包的相關資訊。 [閱讀更多](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)。

## <a name="next-steps"></a>後續步驟
 - 請前往我們的[檔集總覽頁面](https://docs.microsoft.com/azure/network-watcher/)，瞭解一些教學課程，讓您開始使用網路監看員。
