---
title: 有關 Azure 網路監看員 (常見問題) 的常見問題 |Microsoft Docs
description: 本文將回答有關 Azure 網路監看員服務的常見問題。
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
ms.openlocfilehash: 959062d493d9eb47204be2488f216b70804b3605
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965760"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>有關 Azure 網路監看員 (常見問題) 的常見問題
[Azure 網路](./network-watcher-monitoring-overview.md)監看員服務提供一套工具，可用來監視、診斷、查看計量，以及啟用或停用 Azure 虛擬網路中的資源記錄。 本文將回答有關此服務的常見問題。

## <a name="general"></a>一般

### <a name="what-is-network-watcher"></a>什麼是網路監看員？
網路監看員是設計用來監視和修復 IaaS (基礎結構即服務) 元件的網路健康情況，其中包括虛擬機器、虛擬網路、應用程式閘道、負載平衡器，以及 Azure 虛擬網路中的其他資源。 它不是監視 PaaS (平臺即服務) 基礎結構或取得 web/行動分析的解決方案。

### <a name="what-tools-does-network-watcher-provide"></a>網路監看員所提供的工具有哪些？
網路監看員提供三個主要的功能集
* 監視
  * [拓撲視圖](./view-network-topology.md) 會顯示您的虛擬網路中的資源，以及它們之間的關聯性。
  * 連線[監視器](./connection-monitor.md)可讓您監視 VM 與另一個網路資源之間的連線能力和延遲。
  * [網路效能監視器](../azure-monitor/insights/network-performance-monitor.md) 可讓您監視混合式網路架構、Expressroute 線路和服務/應用程式端點之間的連線能力和延遲。  
* 診斷
  * [IP 流量驗證](./network-watcher-ip-flow-verify-overview.md) 可讓您偵測 VM 層級的流量篩選問題。
  * [下一個躍點](./network-watcher-next-hop-overview.md) 可協助您確認流量路由，以及偵測路由問題。
  * 連線[疑難排解](./network-watcher-connectivity-portal.md)可在 VM 與另一個網路資源之間啟用一次性連線和延遲檢查。
  * 封[包捕獲](./network-watcher-packet-capture-overview.md)可讓您在虛擬網路中的 VM 上捕獲所有流量。
  * [Vpn 疑難排解](./network-watcher-troubleshoot-overview.md) 會針對 VPN 閘道和連線執行多個診斷檢查，以協助偵測問題。
* 記錄
  * [NSG 流量記錄](./network-watcher-nsg-flow-logging-overview.md) 可讓您將網路安全性群組中的所有流量記錄 [ (nsg) ](../virtual-network/network-security-groups-overview.md)
  * 使用[分析](./traffic-analytics.md)可處理您的 NSG 流量記錄資料，讓您能夠視覺化、查詢、分析及瞭解您的網路流量。


如需詳細資訊，請參閱網路監看員 [總覽頁面](./network-watcher-monitoring-overview.md)。


### <a name="how-does-network-watcher-pricing-work"></a>網路監看員價格如何運作？
流覽網路監看員元件的 [定價頁面](https://azure.microsoft.com/pricing/details/network-watcher/) 及其定價。

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>哪些區域支援/提供網路監看員？
您可以在[Azure 服務可用性頁面](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)上查看最新的區域可用性

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>使用網路監看員需要哪些許可權？
請參閱使用網路監看員 [所需的 AZURE RBAC 許可權](./required-rbac-permissions.md)清單。 若要部署資源，您需要 NetworkWatcherRG 的參與者許可權 (請參閱以下) 。

### <a name="how-do-i-enable-network-watcher"></a>如何啟用網路監看員？
每個訂用帳戶都會 [自動啟用](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) 網路監看員服務。

### <a name="what-is-the-network-watcher-deployment-model"></a>什麼是網路監看員部署模型？
網路監看員父資源會在每個區域中部署唯一的實例。 命名格式： NetworkWatcher_RegionName。 範例： NetworkWatcher_centralus 是「美國中部」區域的網路監看員資源。

### <a name="what-is-the-networkwatcherrg"></a>什麼是 NetworkWatcherRG？
網路監看員資源位於已自動建立的隱藏 **NetworkWatcherRG** 資源群組中。 例如，NSG 流量記錄資源是網路監看員的子資源，而且會在 NetworkWatcherRG 中啟用。

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>為什麼我需要安裝網路監看員延伸模組？ 
需要產生或攔截 VM 流量的任何功能都需要網路監看員擴充功能。 

### <a name="which-features-require-the-network-watcher-extension"></a>哪些功能需要網路監看員延伸模組？
封包捕獲、連線疑難排解和連線監視器功能都需要有網路監看員延伸模組。

### <a name="what-are-resource-limits-on-network-watcher"></a>網路監看員的資源限制為何？
查看 [ [服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) ] 頁面中的所有限制。  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>為什麼每個區域只允許一個網路監看員實例？ 
網路監看員只需要啟用一次，訂用帳戶的功能才能運作，這不是服務限制。

### <a name="how-can-i-manage-the-network-watcher-resource"></a>如何管理網路監看員資源？ 
網路監看員資源代表網路監看員的後端服務，完全由 Azure 管理。 客戶不需要管理它。 資源不支援移動等作業。 不過，您 [可以刪除資源](./network-watcher-create.md#delete-a-network-watcher-in-the-portal)。 

## <a name="service-availability-and-redundancy"></a>服務可用性和冗余 

### <a name="is-the-network-watcher-service-zone-resilient"></a>網路監看員服務區域是否可復原？ 
是。 網路監看員服務預設為區域復原。 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>如何? 將網路監看員服務設定為具備區域復原能力？ 
不需要客戶設定即可啟用區域復原。 網路監看員資源的區域復原預設為可用，且由服務本身管理。 

## <a name="nsg-flow-logs"></a>NSG 流量記錄

### <a name="what-does-nsg-flow-logs-do"></a>NSG 流量記錄有何用途？
您可以透過 [網路安全性群組 (nsg) ](../virtual-network/network-security-groups-overview.md)來結合和管理 Azure 網路資源。 NSG 流量記錄可讓您透過 Nsg 記錄有關所有流量的5元組流量資訊。 原始流量記錄會寫入 Azure 儲存體帳戶，以便在必要時進行進一步的處理、分析、查詢或匯出。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>如何? 將 NSG 流量記錄與防火牆後方的儲存體帳戶搭配使用？

若要使用位於防火牆後方的儲存體帳戶，您必須針對受信任的 Microsoft 服務提供例外狀況，以存取您的儲存體帳戶：

* 流覽至儲存體帳戶，方法是在入口網站的 [全域搜尋] 中輸入儲存體帳戶的名稱，或在 [[儲存體帳戶] 頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)中輸入儲存體帳戶的名稱。
* 在 [設定] 區段下，選取 [防火牆和虛擬網路]
* 在 [允許存取來源] 中，選取 [ **選取的網路**]。 然後，在 [**例外** 狀況] 下，勾選 [**允許信任的 Microsoft 服務存取此儲存體帳戶**] 旁邊的方塊。 
* 如果早已選取，則不需要再變更。  
* 在 [ [NSG 流量記錄] 總覽頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) 上找出您的目標 NSG，並在選取上述儲存體帳戶的情況啟用 NSG 流量記錄。

您可以在幾分鐘後檢查儲存體記錄，屆時應該就會看到已更新的時間戳記或新建立的 JSON 檔案。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>如何? 將 NSG 流量記錄與服務端點後方的儲存體帳戶搭配使用？

NSG 流量記錄與服務端點相容，而不需要進行任何額外的設定。 請參閱教學課程，以瞭解如何在虛擬網路中 [啟用服務端點](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) 。


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Flow 記錄第1版 & 2 之間有何差異？
流程記錄第2版引進了 *流程狀態* 的概念，& 儲存傳送的位元組和封包的相關資訊。 [閱讀其他資訊](./network-watcher-nsg-flow-logging-overview.md#log-format)。

## <a name="next-steps"></a>後續步驟
 - 請前往我們的 [檔總覽頁面](./index.yml) ，取得一些可協助您開始使用網路監看員的教學課程。