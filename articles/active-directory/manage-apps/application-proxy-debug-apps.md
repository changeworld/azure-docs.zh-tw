---
title: 偵錯工具 Proxy 應用程式-Azure Active Directory |Microsoft Docs
description: Azure Active Directory （Azure AD）應用程式 Proxy 應用程式的 Debug 問題。
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74382064"
---
# <a name="debug-application-proxy-application-issues"></a>針對應用程式 Proxy 應用程式問題進行偵錯 

本文可協助您針對 Azure Active Directory （Azure AD）應用程式 Proxy 應用程式的問題進行疑難排解。 如果您使用應用程式 Proxy 服務來遠端存取內部部署 web 應用程式，但無法連線到應用程式，請使用此流程圖來進行應用程式問題的疑難排解。 

## <a name="before-you-begin"></a>開始之前

針對應用程式 Proxy 問題進行疑難排解時，我們建議您從連接器開始。 首先，請遵循[偵錯工具 Proxy 連接器問題](application-proxy-debug-connectors.md)中的疑難排解流程，確認應用程式 proxy 連接器已正確設定。 如果您仍然遇到問題，請回到本文以對應用程式進行疑難排解。  

如需應用程式 Proxy 的詳細資訊，請參閱：

- [透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy.md)
- [應用程式 Proxy 連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>應用程式問題的流程圖

此流程圖會逐步引導您完成一些連接至應用程式時常見問題的檢查步驟。 如需每個步驟的詳細資訊，請參閱流程圖後面的表格。

![顯示偵錯工具之步驟的流程圖](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | 動作 | 描述 | 
|---------|---------|---------|
|1 | 開啟瀏覽器、存取應用程式，然後輸入您的認證 | 請嘗試使用您的認證登入應用程式，並檢查是否有任何使用者相關的錯誤，例如[無法存取此公司應用程式](application-proxy-sign-in-bad-gateway-timeout-error.md)。 |
|2 | 確認使用者對應用程式的指派 | 請確定您的使用者帳戶具有從公司網路內部存取應用程式的許可權，然後依照[測試應用程式](application-proxy-add-on-premises-application.md#test-the-application)中的步驟來測試登入應用程式。 如果登入問題仍然存在，請參閱如何針對登[入錯誤進行疑難排解](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。  |
|3 | 開啟瀏覽器並嘗試存取應用程式 | 如果立即出現錯誤，請檢查是否已正確設定應用程式 Proxy。 如需特定錯誤訊息的詳細資訊，請參閱針對[應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)。  |
|4 | 檢查您的自訂網域設定或針對錯誤進行疑難排解 | 如果頁面完全沒有顯示，請確認您的自訂網域已正確設定，方法是[使用自訂網域](application-proxy-configure-custom-domain.md)進行審核。<br></br>如果未載入頁面，並出現錯誤訊息，請參考[疑難排解應用程式 Proxy 問題和錯誤訊息](application-proxy-troubleshoot.md)，以進行錯誤的疑難排解。 <br></br>如果出現錯誤訊息所需的時間超過20秒，可能會發生連線問題。 請移至[偵錯工具 Proxy 連接器](application-proxy-debug-connectors.md)疑難排解一文。  |
|5 | 如果問題持續發生，請移至連接器的調試 | Proxy 與連接器之間或連接器與後端之間可能有連線問題。 請移至[偵錯工具 Proxy 連接器](application-proxy-debug-connectors.md)疑難排解一文。 |
|6 | 發佈所有資源、檢查瀏覽器開發人員工具，以及修正連結 | 請確定發行路徑包含您應用程式的所有必要影像、腳本和樣式表單。 如需詳細資訊，請參閱[將內部部署應用程式新增至 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 <br></br>使用瀏覽器的開發人員工具（Internet Explorer 或 Microsoft Edge 中的 F12 工具）並檢查發佈問題，如[應用程式頁面未正確顯示](application-proxy-page-appearance-broken-problem.md)所述。 <br></br>在[頁面上的連結](application-proxy-page-links-broken-problem.md)中解決中斷連結的選項無法使用。 |
|7 | 檢查網路延遲 | 如果頁面載入速度很慢，請瞭解在[降低延遲的考慮](application-proxy-network-topology.md#considerations-for-reducing-latency)下，將網路延遲降至最低的方法。 | 
|8 | 請參閱其他疑難排解說明 | 如果問題持續發生，請在[應用程式 Proxy 疑難排解檔](application-proxy-troubleshoot.md)中尋找其他疑難排解文章。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](application-proxy-register-connector-powershell.md)
