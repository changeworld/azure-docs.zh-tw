---
title: Azure AD 應用程式 Proxy：版本發行歷程記錄
description: 本文列出 Azure AD 應用程式 Proxy 的所有版本，並說明新功能和已修正的問題
services: active-directory
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/22/2020
ms.subservice: app-mgmt
ms.author: kenwith
ms.openlocfilehash: 3f6b1bb189390f6ddd02e9ea2d636dcaa66a4f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88210855"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 應用程式 Proxy：版本發行歷程記錄
本文列出已發行的 Azure Active Directory (Azure AD) 應用程式 Proxy 的版本和功能。 Azure AD 團隊會定期以新的特性和功能更新應用程式 Proxy。 發行新版本時，會自動更新應用程式 Proxy 連接器。 

建議您確定您的連接器已啟用自動更新，以確保您有最新的功能和錯誤修正。 Microsoft 可直接支援最新連接器版本和一個版本。

以下是相關資源的清單：

資源 |  詳細資料
--------- | --------- |
如何啟用應用程式 Proxy | 本 [教學](application-proxy-add-on-premises-application.md)課程會說明啟用應用程式 Proxy 以及安裝和註冊連接器的先決條件。
了解 Azure AD 應用程式 Proxy 連接器 | 深入瞭解 [連接器管理](application-proxy-connectors.md) 以及連接器如何 [自動升級](application-proxy-connectors.md#automatic-updates)。
Azure AD 應用程式 Proxy 連接器下載 |  [下載最新的連接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>發行狀態

2020年7月22日：發行為下載此版本，僅可透過下載頁面進行安裝。 此版本的自動升級版本將于稍後發行。

### <a name="new-features-and-improvements"></a>新功能和改進
-   改進對 Azure Government 雲端環境的支援。 如需如何正確安裝 Azure Government cloud 連接器的步驟，請參閱 [先決條件](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls) 和 [安裝步驟](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud)。
- 支援將遠端桌面服務 web 用戶端與應用程式 Proxy 搭配使用。 如需詳細資訊，請參閱 [使用 Azure AD 應用程式 Proxy 發佈遠端桌面](application-proxy-integrate-with-remote-desktop-services.md) 。
- 改進 websocket 延伸模組的協商。 

### <a name="fixed-issues"></a>已修正的問題
- 修正強制小寫字串的 websocket 問題。
- 修正導致連接器有時候沒有回應的問題。

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>發行狀態

2020年7月17日：已發行以供下載。 此版本僅可透過下載頁面進行安裝。 此版本的自動升級版本將于稍後發行。

### <a name="fixed-issues"></a>已修正的問題
- 先前版本中的已解決記憶體流失問題
- Websocket 支援的一般改進

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>發行狀態

2020年4月07日：已發行以供下載

### <a name="new-features-and-improvements"></a>新功能和改進
-   連接器只會使用 TLS 1.2 進行所有連接。 如需詳細資訊，請參閱 [連接器先決條件](application-proxy-add-on-premises-application.md#prerequisites) 。
- 已改善連接器與 Azure 服務之間的信號。 這包括支援在連接器與 Azure 服務之間進行 WCF 通訊的可靠會話，以及 WebSocket 通訊的 DNS 快取改善。
- 支援在連接器和後端應用程式之間設定 proxy。 如需詳細資訊，請參閱 [使用現有的內部部署 proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="fixed-issues"></a>已修正的問題
- 已移除回復至埠8080，以進行從連接器到 Azure 服務的通訊。
- 已新增 WebSocket 通訊的偵錯工具追蹤。 
- 已解決在後端應用程式 cookie 上設定時保留 SameSite 屬性的問題。

## <a name="156120"></a>1.5.612.0 版

### <a name="release-status"></a>發行狀態

2018年9月20日：已發行供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 已新增 QlikSense 應用程式的 WebSocket 支援。 若要深入瞭解如何整合 QlikSense 與應用程式 Proxy，請參閱本 [逐步](application-proxy-qlik.md)解說。 
- 改進了安裝精靈，讓您更輕鬆地設定輸出 proxy。 
- 將 TLS 1.2 設定為連接器的預設通訊協定。 
- 新增 End-User 授權合約 (EULA) 。  

### <a name="fixed-issues"></a>已修正的問題

- 修正了連接器中造成記憶體流失的 bug。
- 已更新 Azure 服務匯流排版本，其中包含連接器超時問題的錯誤修正。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>發行狀態

2018年1月19日：已發行供下載

### <a name="fixed-issues"></a>已修正的問題

- 已新增在 cookie 中需要網域翻譯的自訂網域支援。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>發行狀態 

2017年5月25日：已發行以供下載 

### <a name="new-features-and-improvements"></a>新功能和改進 

改善對連接器的輸出連接限制的控制。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>發行狀態

2017年4月15日：已發行供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 使用較少的必要端口簡化上架和管理。 應用程式 Proxy 現在只需要開啟兩個標準輸出埠：443和80。 應用程式 Proxy 只會繼續使用輸出連線，因此您仍然不需要 DMZ 中的任何元件。 如需詳細資訊，請參閱我們的設定 [檔](application-proxy-add-on-premises-application.md)。  
- 如果您的外部 proxy 或防火牆支援，您現在可以透過 DNS 開啟您的網路，而不是使用 IP 範圍。 應用程式 Proxy 服務只需要連接到 *. msappproxy.net 和 *. servicebus.windows.net。


## <a name="earlier-versions"></a>較早的版本

如果您使用的應用程式 Proxy 連接器版本早于1.5.36.0，請更新至最新版本，以確保您擁有最新的完整支援功能。

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [如何透過 Azure AD 應用程式 Proxy 遠端存取內部部署應用程式](application-proxy.md)。
- 若要開始使用應用程式 Proxy，請參閱 [教學課程：新增內部部署應用程式，以透過應用程式 proxy 進行遠端存取](application-proxy-add-on-premises-application.md)。
