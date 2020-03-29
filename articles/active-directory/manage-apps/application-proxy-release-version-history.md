---
title: Azure AD 應用程式代理：版本發佈歷史記錄 |微軟文檔
description: 本文列出了 Azure AD 應用程式代理的所有版本，並介紹了新功能和修復問題
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68693908"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 應用程式代理：版本發佈歷史記錄
本文列出了已發佈的 Azure 活動目錄 （Azure AD） 應用程式代理的版本和功能。 Azure AD 團隊定期更新具有新功能和功能的應用程式代理。 發佈新版本時，應用程式代理連接器將自動更新。 

我們建議您確保為連接器啟用自動更新，以確保您具有最新的功能和錯誤修復。 Microsoft 提供對上次連接器版本和之前一個版本的直接支援。

下面是相關資源的清單：

資源 |  詳細資料
--------- | --------- |
如何啟用應用程式代理 | [本教程](application-proxy-add-on-premises-application.md)介紹了啟用應用程式代理以及安裝和註冊連接器的先決條件。
了解 Azure AD 應用程式 Proxy 連接器 | 詳細瞭解[連接器管理](application-proxy-connectors.md)以及如何[連接器自動升級](application-proxy-connectors.md#automatic-updates)。
Azure AD 應用程式代理連接器下載 |  [下載最新的連接器](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)。

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>發行狀態

2018 年 9 月 20 日：發佈供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 添加了對 QlikSense 應用程式的 WebSocket 支援。 要瞭解有關如何將 QlikSense 與應用程式代理集成的更多，請參閱本[演練](application-proxy-qlik.md)。 
- 改進了安裝精靈，以便更輕鬆地配置出站代理。 
- 將 TLS 1.2 設置為連接器的預設協定。 
- 添加了新的使用者授權合約 （EULA）。  

### <a name="fixed-issues"></a>已修正的問題

- 修復了導致連接器中某些記憶體洩漏的 Bug。
- 更新了 Azure 服務匯流排版本，其中包括連接器超時問題的錯誤修復程式。

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>發行狀態

2018 年 1 月 19 日：發佈供下載

### <a name="fixed-issues"></a>已修正的問題

- 添加了對需要 Cookie 中域轉換的自訂域的支援。

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>發行狀態 

2017 年 5 月 25 日：發佈供下載 

### <a name="new-features-and-improvements"></a>新功能和改進 

改進了對連接器出站連接限制的控制。 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>發行狀態

2017 年 4 月 15 日：發佈供下載

### <a name="new-features-and-improvements"></a>新功能和改進

- 簡化的載入和管理，減少所需的埠。 應用程式代理現在只需要打開兩個標準出站埠：443 和 80。 應用程式代理繼續僅使用出站連接，因此您仍不需要 DMZ 中的任何元件。 有關詳細資訊，請參閱我們的 [配置文檔](application-proxy-add-on-premises-application.md)。  
- 如果外部代理或防火牆支援，您現在可以通過 DNS 而不是 IP 範圍打開網路。 應用程式代理服務僅需要連接到 *.msappproxy.net 和 *.servicebus.windows.net。


## <a name="earlier-versions"></a>較早的版本

如果您使用的是應用程式代理連接器版本早于 1.5.36.0，請更新到最新版本以確保您擁有最新的完全支援的功能。

## <a name="next-steps"></a>後續步驟
- 通過[Azure AD 應用程式代理瞭解有關遠端存取本地應用程式](application-proxy.md)的更多資訊。
- 要開始使用應用程式代理，請參閱[教程：添加本地應用程式，以便通過應用程式代理進行遠端存取](application-proxy-add-on-premises-application.md)。
