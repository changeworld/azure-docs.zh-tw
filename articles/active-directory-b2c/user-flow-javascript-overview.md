---
title: JavaScript 和頁面配置版本
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure 活動目錄 B2C 中啟用 JavaScript 和使用頁面配置版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185826"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 中的 JavaScript 和頁面配置版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 為使用者流和自訂策略中的使用者介面元素提供了一組包含 HTML、CSS 和 JavaScript 的打包內容。

要為應用程式啟用 JavaScript，

* 使用 Azure 門戶在使用者流上啟用它
* 選擇[頁面配置](page-layout.md)
* 在請求中使用[b2clogin.com](b2clogin.md)

如果要啟用[JavaScript](javascript-samples.md)用戶端代碼，則 JavaScript 的基礎元素必須是不可變的。 如果它們不是不可改變的，則任何更改都可能導致使用者頁面上出現意外行為。 要防止出現這些問題，請強制使用頁面配置並指定頁面配置版本，以確保基於 JavaScript 的內容定義不可變。 即使您不打算啟用 JavaScript，也可以為頁面指定頁面配置版本。

## <a name="enable-javascript"></a>啟用 JavaScript

在使用者流**屬性**中，可以啟用 JavaScript。 啟用 JavaScript 還強制使用頁面配置。 然後，您可以設置使用者流的頁面配置版本，如下一節所述。

![突出顯示啟用 JavaScript 設置的使用者流屬性頁](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>選擇頁面配置版本

無論您是否在使用者流的屬性中啟用 JavaScript，都可以為使用者流頁指定頁面配置版本。 打開使用者流並選擇**頁面配置**。 在 **"佈局名稱"** 下，選擇使用者流頁並選擇**頁面配置版本**。

有關不同頁面配置版本的資訊，請參閱[頁面配置版本更改日誌](page-layout.md)。

![門戶中的頁面配置設置顯示頁面配置版本下拉清單](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>後續步驟

您可以在 JavaScript 示例中找到 JavaScript 使用的示例[，以在 Azure 活動目錄 B2C 中使用](javascript-samples.md)。
