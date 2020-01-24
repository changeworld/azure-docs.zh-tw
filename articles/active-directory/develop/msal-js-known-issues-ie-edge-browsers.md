---
title: Internet Explorer & Microsoft Edge 的問題（MSAL） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解在使用 Internet Explorer 和 Microsoft Edge 瀏覽器的適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）時的已知問題。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696090"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 和 Microsoft Edge 瀏覽器（MSAL）的已知問題

## <a name="issues-due-to-security-zones"></a>因安全性區域而產生的問題
在 IE 和 Microsoft Edge 中，我們有多個驗證問題的報告（自*Microsoft edge 瀏覽器版本更新至 40.15063.0.0*）。 我們正在追蹤這些專案，並告知 Microsoft Edge 團隊。 雖然 Microsoft Edge 是以解決方案為依據，但以下是經常發生之問題的描述，以及可實行的可能因應措施。

### <a name="cause"></a>原因
這大部分問題的原因如下所示。 會話儲存體和本機儲存體會依 Microsoft Edge 瀏覽器中的安全性區域進行分割。 在此特定版本的 Microsoft Edge 中，當應用程式在區域之間重新導向時，會清除會話儲存體和本機儲存體。 具體而言，在一般瀏覽器導覽中會清除會話儲存體，而且會話和本機儲存體都會在瀏覽器的 InPrivate 模式中清除。 MSAL 會將特定狀態儲存在會話儲存體中，並依賴在驗證流程期間檢查此狀態。 清除會話儲存體時，此狀態會遺失，因而導致中斷的體驗。

### <a name="issues"></a>問題

- **在驗證期間，無限重新導向迴圈和頁面重載**。 當使用者在 Microsoft Edge 上登入應用程式時，會從 AAD 登入頁面重新導向，並停留在無限重新導向迴圈中，而導致重複頁面重載。 這通常會伴隨會話儲存體中的 `invalid_state` 錯誤。

- **無限的取得 token 迴圈和 AADSTS50058 錯誤**。 當在 Microsoft Edge 上執行的應用程式嘗試取得資源的權杖時，應用程式可能會停滯在取得權杖呼叫的無限迴圈中，並在網路追蹤中從 AAD 產生下列錯誤：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **使用登入透過快顯進行驗證時，快顯視窗不會關閉或停滯**。 透過 Microsoft Edge 或 IE （InPrivate）中的快顯視窗進行驗證時，在輸入認證並登入之後，如果流覽中包含多個跨安全性區域的網域，則不會關閉快顯視窗，因為 MSAL 會失去的控制碼。快顯視窗。  

### <a name="update-fix-available-in-msaljs-023"></a>更新： MSAL 中提供的修正程式0.2。3
已在[MSAL 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)中發行驗證重新導向迴圈問題的修正程式。 啟用 MSAL 中的旗標 `storeAuthStateInCookie`，以利用此修正程式。 根據預設，此旗標設定為 false。

啟用 `storeAuthStateInCookie` 旗標時，MSAL 將會使用瀏覽器 cookie 來儲存驗證流程所需的要求狀態。

> [!NOTE]
> 此修正尚未提供給 msal 角和 msal-angularjs 包裝函式。 此修正程式無法解決快顯視窗的問題。

請使用下面的因應措施。

#### <a name="other-workarounds"></a>其他因應措施
請務必測試您的問題是否只發生在特定版本的 Microsoft Edge 瀏覽器上，並在採用這些因應措施之前，在其他瀏覽器上運作。  
1. 在解決這些問題的第一個步驟中，請確定在瀏覽器的安全性設定中，已將應用程式域、和任何其他與驗證流程重新導向相關的網站新增為信任的網站，使其屬於相同的安全性區域。
若要這樣做，請依照下列步驟執行：
    - 開啟**Internet Explorer** ，然後按一下右上角的 [**設定**] （齒輪圖示）
    - 選取**網際網路選項**
    - 選取 [**安全性**] 索引標籤
    - 在 [**信任的網站**] 選項底下，按一下 [**網站**] 按鈕，並在開啟的對話方塊中新增 url。

2. 如先前所述，由於只有會話儲存體會在一般導覽期間清除，因此您可以將 MSAL 設定為使用本機儲存體。 這可以在初始化 MSAL 時設定為 `cacheLocation` config 參數。

請注意，這不會解決 InPrivate 瀏覽的問題，因為會話和本機儲存體都已清除。

## <a name="issues-due-to-popup-blockers"></a>快顯視窗封鎖器的問題

在某些情況下，IE 或 Microsoft Edge 中會封鎖快顯視窗，例如當多重要素驗證期間發生第二個快顯視窗時。 您會在瀏覽器中收到一則通知，讓您只允許快顯視窗一次或一律出現。 如果您選擇允許，瀏覽器會自動開啟快顯視窗，並傳回其 `null` 控制碼。 因此，程式庫沒有視窗的控制碼，而且沒有任何方法可以關閉快顯視窗。 當 Chrome 提示您允許使用快顯視窗時，不會發生相同的問題，因為它不會自動開啟快顯視窗。

因應措施是，開發人員必須先允許 IE 和 Microsoft Edge 中的彈出**視窗，才能**開始使用其應用程式來避免此問題。

## <a name="next-steps"></a>後續步驟
深入瞭解如何[在 Internet Explorer 中使用 MSAL](msal-js-use-ie-browser.md)。
