---
title: 調試應用程式代理應用程式 - Azure 活動目錄 |微軟文檔
description: 調試 Azure 活動目錄 （Azure AD） 應用程式代理應用程式的調試問題。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382064"
---
# <a name="debug-application-proxy-application-issues"></a>針對應用程式 Proxy 應用程式問題進行偵錯 

本文可説明您解決 Azure 活動目錄 （Azure AD） 應用程式代理應用程式的問題。 如果您使用應用程式代理服務遠端存取本地 Web 應用程式，但連接到應用程式時遇到問題，請使用此流程圖調試應用程式問題。 

## <a name="before-you-begin"></a>開始之前

在排除應用程式代理問題時，我們建議您從連接器開始。 首先，按照[調試應用程式代理連接器問題的](application-proxy-debug-connectors.md)故障排除流進行操作，以確保正確配置應用程式代理連接器。 如果仍有問題，請返回到本文對應用程式進行故障排除。  

有關應用程式代理的詳細資訊，請參閱：

- [通過應用程式代理遠端存取本地應用程式](application-proxy.md)
- [應用程式代理連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>應用程式問題的流程圖

此流程圖將引導您完成調試連接到應用程式的一些更常見問題的步驟。 有關每個步驟的詳細資訊，請參閱流程圖下面的表。

![顯示調試應用程式步驟的流程圖](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 動作 | 描述 | 
|---------|---------|---------|
|1 | 打開瀏覽器、訪問應用並輸入憑據 | 嘗試使用憑據登錄到應用，並檢查是否存在任何與使用者相關的錯誤，如[無法訪問此公司應用](application-proxy-sign-in-bad-gateway-timeout-error.md)。 |
|2 | 驗證使用者分配到應用 | 請確保您的使用者帳戶具有從公司網路內部訪問應用的許可權，然後按照[測試應用程式](application-proxy-add-on-premises-application.md#test-the-application)中的步驟測試登錄到應用。 如果登錄問題仍然存在，請參閱[如何排除登錄錯誤](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。  |
|3 | 打開瀏覽器並嘗試訪問應用 | 如果立即出現錯誤，請檢查應用程式代理配置是否正確。 有關特定錯誤訊息的詳細資訊，請參閱[解決應用程式代理問題和錯誤訊息](application-proxy-troubleshoot.md)。  |
|4 | 檢查自訂域設置或排除錯誤 | 如果頁面根本不顯示，請確保通過查看[使用自訂域正確配置自訂域](application-proxy-configure-custom-domain.md)。<br></br>如果頁面未載入並出現錯誤訊息，請參考[疑難排解應用程式代理問題和錯誤訊息](application-proxy-troubleshoot.md)來排除錯誤。 <br></br>如果出現錯誤訊息的時間超過 20 秒，則可能會出現連接問題。 轉到[調試應用程式代理連接器](application-proxy-debug-connectors.md)故障排除一文。  |
|5 | 如果問題仍然存在，則轉到連接器調試 | 代理和連接器之間或連接器和後端之間可能有連接問題。 轉到[調試應用程式代理連接器](application-proxy-debug-connectors.md)故障排除一文。 |
|6 | 發佈所有資源、檢查瀏覽器開發人員工具並修復連結 | 確保發佈路徑包含應用程式所需的所有圖像、腳本和樣式表。 有關詳細資訊，請參閱[將本地應用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 <br></br>使用瀏覽器的開發人員工具（Internet Explorer 或 Microsoft Edge 中的 F12 工具），並檢查應用程式頁中描述的發佈問題[是否正確](application-proxy-page-appearance-broken-problem.md)顯示。 <br></br>查看用於解決[頁面上連結](application-proxy-page-links-broken-problem.md)中斷開的連結的選項不起作用。 |
|7 | 檢查網路延遲 | 如果頁面載入緩慢，請瞭解如何在"注意事項"中最小化網路[延遲，以減少延遲](application-proxy-network-topology.md#considerations-for-reducing-latency)。 | 
|8 | 查看其他故障排除説明 | 如果問題仍然存在，請在[應用程式代理故障排除文檔中](application-proxy-troubleshoot.md)查找其他故障排除文章。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何靜默安裝 Azure AD 應用程式代理連接器](application-proxy-register-connector-powershell.md)
