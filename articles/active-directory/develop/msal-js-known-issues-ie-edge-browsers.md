---
title: Internet Explorer 和 Microsoft Edge (MSAL.js) 的問題 | Azure
titleSuffix: Microsoft identity platform
description: 了解在搭配使用適用於 JavaScript 的 Microsoft 驗證程式庫 (MSAL.js) 與 Internet Explorer 和 Microsoft Edge 瀏覽器時的已知問題。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83772075"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 和 Microsoft Edge 瀏覽器 (MSAL.js) 的已知問題

## <a name="issues-due-to-security-zones"></a>因安全性區域而產生的問題
我們收到多份關於 IE 和 Microsoft Edge 驗證問題的報告 (自 *Microsoft Edge 瀏覽器版本更新為 40.15063.0.0* 起)。 我們正在追蹤這些問題，並已告知 Microsoft Edge 小組。 Microsoft Edge 正在尋找解決方案，在此同時，以下是經常發生問題的描述，以及可實作的可能因應措施。

### <a name="cause"></a>原因
這些問題的原因大多如下。 在 Microsoft Edge 瀏覽器中，工作階段儲存體和本機儲存體會依安全性區域來劃分。 在這個特定的 Microsoft Edge 版本中，當應用程式跨區域重新導向時，工作階段儲存體和本機儲存體會遭到清除。 具體而言，一般瀏覽器導覽會清除工作階段儲存體，瀏覽器的 InPrivate 模式則會清除工作階段儲存體和本機儲存體。 MSAL.js 會將特定狀態儲存在工作階段儲存體中，並依賴在驗證流程期間檢查此狀態。 工作階段儲存體遭到清除時，此狀態會遺失，因而導致不良體驗。

### <a name="issues"></a>問題

- **驗證期間會發生無限的重新導向迴圈和頁面重新載入**。 使用者在 Microsoft Edge 上登入應用程式時，會從 AAD 登入頁面重新導向回來，並陷入無限的重新導向迴圈而導致重複地重新載入頁面。 這通常會伴隨工作階段儲存體中的 `invalid_state` 錯誤。

- **無限的取得權杖迴圈和 AADSTS50058 錯誤**。 當 Microsoft Edge 上執行的應用程式嘗試取得資源的權杖時，應用程式可能會陷入無限的取得權杖呼叫迴圈，並在網路追蹤中出現來自 AAD 的下列錯誤：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **透過快顯使用登入來進行驗證時，快顯視窗不會關閉或是會卡住**。 在 Microsoft Edge 或 IE (InPrivate) 中透過快顯視窗進行驗證時，於輸入認證並登入之後，如果導覽中涉及跨安全性區域的多個網域，則因為 MSAL.js 會失去快顯視窗的掌控能力，因此快顯視窗不會關閉。  

### <a name="update-fix-available-in-msaljs-023"></a>更新：MSAL.js 0.2.3 中可用的修正程式
[MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 中已發行驗證重新導向迴圈問題的修正程式。 請在 MSAL.js 設定中啟用 `storeAuthStateInCookie` 旗標以利用此修正程式。 根據預設，此旗標會設定為 false。

當 `storeAuthStateInCookie` 旗標啟用時，MSAL.js 會使用瀏覽器 Cookie 來儲存驗證流程要進行驗證所需的要求狀態。

> [!NOTE]
> 此修正程式尚未提供給 msal-angular 和 msal-angularjs 包裝函式。 此修正程式無法解決快顯視窗的問題。

請使用下面的因應措施。

#### <a name="other-workarounds"></a>其他因應措施
請務必測試您的問題是否只發生在特定版本的 Microsoft Edge 瀏覽器上，並在採用這些因應措施之前，先在其他瀏覽器上運作看看。  
1. 解決這些問題的第一個步驟是確定瀏覽器的安全性設定中，已將應用程式定義域以及驗證流程重新導向所涉及的任何其他網站新增為信任的網站，使其屬於相同的安全性區域。
若要這樣做，請依照下列步驟執行：
    - 開啟 **Internet Explorer**，然後按一下右上角的 [設定] (齒輪圖示)
    - 選取 [網際網路選項]
    - 選取 [安全性] 索引標籤
    - 在 [信任的網站] 選項底下，按一下 [網站] 按鈕，然後在開啟的對話方塊中新增 URL。

2. 如先前所述，由於一般導覽期間只會清除工作階段儲存體，因此您可以改為將 MSAL.js 設定為使用本機儲存體。 這可以在初始化 MSAL 時設定為 `cacheLocation` 設定參數。

請注意，這不會解決 InPrivate 瀏覽的問題，因為工作階段和本機儲存體都已清除。

## <a name="issues-due-to-popup-blockers"></a>快顯封鎖程式的問題

在某些情況下，IE 或 Microsoft Edge 中的快顯視窗會遭到封鎖，例如當第二個快顯視窗在[多重要素驗證](../authentication/concept-mfa-howitworks.md)期間發生時。 您會在瀏覽器中收到警示，詢問您是要只允許快顯視窗一次還是一律允許。 如果您選擇允許，瀏覽器就會自動開啟快顯視窗，並傳回其 `null` 控制代碼。 因此，程式庫沒有視窗的控制代碼，也沒有任何辦法可以關閉快顯視窗。 當 Chrome 提示您允許快顯視窗時卻不會發生相同的問題，因為 Chrome 不會自動開啟快顯視窗。

**因應措施**是，開發人員必須先在 IE 和 Microsoft Edge 中允許快顯視窗再開始使用其應用程式，以避免此問題。

## <a name="next-steps"></a>後續步驟
深入了解如何[在 Internet Explorer 中使用 MSAL.js](msal-js-use-ie-browser.md)。
