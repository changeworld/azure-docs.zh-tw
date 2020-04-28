---
title: Internet Explorer 的問題（MSAL） |Azure
titleSuffix: Microsoft identity platform
description: 搭配 Internet Explorer 瀏覽器使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695852"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 和 Microsoft Edge 瀏覽器（MSAL）的已知問題

系統會針對[JAVASCRIPT ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29)產生適用于 javascript 的 Microsoft 驗證程式庫（MSAL），讓它可以在 Internet Explorer 中執行。 不過，有幾件事需要知道。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet Explorer 中執行應用程式
如果您想要在可于 Internet Explorer 中執行的應用程式中使用 MSAL，您必須先新增對承諾 polyfill 的參考，然後再參考 MSAL。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

這是因為 Internet Explorer 不支援原生的 JavaScript 承諾。

## <a name="debugging-an-application-running-in-internet-explorer"></a>在 Internet Explorer 中執行應用程式的偵錯工具

### <a name="running-in-production"></a>在生產環境中執行
將您的應用程式部署至生產環境（例如在 Azure Web apps 中）通常可以正常運作，但前提是使用者已接受快顯視窗。 我們已使用 Internet Explorer 11 進行測試。

### <a name="running-locally"></a>在本機執行
如果您想要在本機執行應用程式，並在 Internet Explorer 中進行 debug，您必須注意下列考慮（假設您想要以執行應用程式的方式*http://localhost:1234*）：

- Internet Explorer 具有名為「受保護模式」的安全性機制，可防止 MSAL 正確運作。 在您登入後的徵兆中，可以將頁面重新導向至http://localhost:1234/null。

- 若要在本機執行和偵錯工具，您必須停用此「受保護模式」。 針對此：

    1. 按一下 [Internet Explorer**工具**] （齒輪圖示）。
    1. 依序選取 [**網際網路選項**] 和 [**安全性**] 索引標籤。
    1. 按一下 [**網際網路**] 區域，然後取消核取 [**啟用受保護模式（需要重新開機 Internet Explorer）]**。 Internet Explorer 會警告您的電腦已不再受到保護。 按一下 [確定]  。
    1. 重新開機 Internet Explorer。
    1. 執行應用程式並對其進行 debug。

當您完成時，請還原 Internet Explorer 安全性設定。  選取 [**設定** -> ] [**網際網路選項** -> **Security** -> ] [**將所有區域重設為預設層級**]。

## <a name="next-steps"></a>後續步驟
深入瞭解[在 Internet Explorer 中使用 MSAL 時的已知問題](msal-js-use-ie-browser.md)。