---
title: 互聯網瀏覽器&微軟邊緣 （MSAL.js） 的問題 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解使用適用于 Internet 資源管理器和 Microsoft 邊緣瀏覽器的 JavaScript 的 Microsoft 身份驗證庫 （MSAL.js） 時的問題。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696090"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>在 Internet 資源管理器和微軟邊緣瀏覽器 （MSAL.js） 上的已知問題

## <a name="issues-due-to-security-zones"></a>由於安全區域引起的問題
我們在 IE 和 Microsoft Edge 中有多個身份驗證問題報告（自將*Microsoft Edge 瀏覽器版本更新到 40.15063.0.0）* 以來。 我們正在跟蹤這些，並通知了微軟邊緣團隊。 雖然 Microsoft Edge 致力於解決問題，但下面是常見問題和可能實現的解決方法的說明。

### <a name="cause"></a>原因
造成大多數這些問題的原因如下。 會話存儲和本機存放區由 Microsoft 邊緣瀏覽器中的安全區域分區。 在此特定版本的 Microsoft Edge 中，當應用程式跨區域重定向時，會話存儲和本機存放區將被清除。 具體而言，會話存儲在常規瀏覽器導航中清除，會話和本機存放區在瀏覽器的 InPrivate 模式下清除。 MSAL.js 在會話存儲中保存某些狀態，並依賴于在身份驗證流期間檢查此狀態。 清除會話存儲後，此狀態將丟失，從而導致體驗中斷。

### <a name="issues"></a>問題

- **在身份驗證期間，無限重定向迴圈和頁面重新載入**。 當使用者登錄到 Microsoft Edge 上的應用程式時，他們將從 AAD 登錄頁重定向回來，並卡在無限重定向迴圈中，從而導致重複的頁面重新載入。 這通常伴隨著會話存儲中`invalid_state`的錯誤。

- **無限獲取權杖迴圈和 AADSTS50058 錯誤**。 當在 Microsoft Edge 上運行的應用程式嘗試獲取資源的權杖時，應用程式可能會卡在獲取權杖調用的無限迴圈中，同時網路跟蹤中的 AAD 出現以下錯誤：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **快顯視窗不會關閉或卡住時，使用登錄通過快顯視窗進行身份驗證**。 通過 Microsoft Edge 或 IE（InPrivate）中的快顯視窗進行身份驗證時，在輸入憑據並登錄後，如果導航中涉及多個跨安全區域的域，則快顯視窗不會關閉，因為 MSAL.js 會丟失對快顯視窗。  

### <a name="update-fix-available-in-msaljs-023"></a>更新： MSAL.js 0.2.3 中的修復可用
[MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)中發佈了身份驗證重定向迴圈問題的修復程式。 啟用 MSAL.js 配置中的標誌`storeAuthStateInCookie`以利用此修復程式。 預設情況下，此標誌設置為 false。

啟用`storeAuthStateInCookie`標誌後，MSAL.js 將使用瀏覽器 Cookie 來存儲驗證身份驗證流所需的請求狀態。

> [!NOTE]
> 此修復程式尚不適用於 msal 角和 msal 角 js 包裝器。 此修復程式不能解決快顯視窗的問題。

使用下面的解決方法。

#### <a name="other-workarounds"></a>其他解決方法
在採用這些解決方法之前，請確保測試您的問題僅在 Microsoft 邊緣瀏覽器的特定版本上發生，並在其他瀏覽器上工作。  
1. 作為解決這些問題的第一步，請確保在瀏覽器的安全設置中將應用程式域、和涉及身份驗證流重定向的任何其他網站添加為受信任的網站，以便它們屬於同一安全區域。
若要這樣做，請依照下列步驟執行：
    - 打開**Internet 資源管理器**並按一下右上角的**設置**（齒輪圖示）
    - 選擇**互聯網選項**
    - 選擇 **"安全**"選項卡
    - 在 **"受信任的網站"** 選項下，按一下**網站**按鈕，並在打開的對話方塊中添加 URL。

2. 如前所述，由於在常規導航期間僅清除會話存儲，因此您可以配置 MSAL.js 以改用本機存放區。 這可以設置為配置參數，`cacheLocation`同時初始化 MSAL。

請注意，這不能解決 InPrivate 瀏覽的問題，因為會話和本機存放區都已清除。

## <a name="issues-due-to-popup-blockers"></a>彈出阻止器引起的問題

在某些情況下，在 IE 或 Microsoft 邊緣中阻止快顯視窗，例如，在多重要素驗證期間出現第二個快顯視窗。 您將在瀏覽器中收到警報，以允許快顯視窗一次或始終。 如果選擇允許，瀏覽器將自動打開快顯視窗並返回該視窗的`null`控制碼。 因此，庫沒有視窗的控制碼，並且無法關閉快顯視窗。 Chrome 會提示您允許快顯視窗，因為它不會自動打開快顯視窗，因此不會出現同樣的問題。

作為**解決方法**，開發人員在開始使用其應用以避免此問題之前，需要允許 IE 和 Microsoft Edge 中的快顯視窗。

## <a name="next-steps"></a>後續步驟
瞭解有關在[Internet 資源管理器中使用 MSAL.js](msal-js-use-ie-browser.md)的更多資訊。
