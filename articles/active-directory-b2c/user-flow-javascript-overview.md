---
title: JavaScript 和頁面配置版本
titleSuffix: Azure AD B2C
description: 瞭解如何啟用 JavaScript，並在 Azure Active Directory B2C 中使用頁面配置版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.custom: project-no-code, devx-track-javascript
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d85ffca8c72e7a247d5bbd1e593335fd7995b594
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170147"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和頁面配置版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 為使用者流程和自訂原則中的使用者介面元素，提供一組封裝內容，其中包含 HTML、CSS 和 JavaScript。

若要為您的應用程式啟用 JavaScript：

* 使用 Azure 入口網站，在使用者流程上啟用它
* 選取[頁面配置](page-layout.md)
* 在您的要求中使用[b2clogin.com](b2clogin.md)

如果您想要啟用[javascript](javascript-samples.md)用戶端程式代碼，則您的 javascript 所依據的元素必須是不可變的。 如果它們不是固定的，任何變更都可能會在使用者頁面上造成非預期的行為。 若要避免這些問題，請強制使用頁面配置，並指定頁面配置版本，以確保您的 JavaScript 所依據的內容定義是不可變的。 即使您不想要啟用 JavaScript，您也可以為頁面指定頁面配置版本。

## <a name="enable-javascript"></a>啟用 JavaScript

在 [使用者流程]**屬性**中，您可以啟用 JavaScript。 啟用 JavaScript 也會強制使用頁面配置。 接著，您可以設定使用者流程的頁面配置版本，如下一節所述。

![反白顯示啟用 JavaScript 設定的 [使用者流程屬性] 頁面](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>選取頁面配置版本

無論您是否在使用者流程的屬性中啟用 JavaScript，都可以為使用者流程頁面指定頁面配置版本。 開啟使用者流程，然後選取 [**頁面配置**]。 在 [**版面配置名稱**] 底下，選取 [使用者流程] 頁面，然後選擇**頁面配置版本**。

如需不同頁面配置版本的詳細資訊，請參閱[頁面配置版本變更記錄](page-layout.md)檔。

![入口網站中顯示頁面配置版本下拉式清單的頁面配置設定](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>後續步驟

您可以在 JAVAscript 範例中找到 JavaScript 使用方式的範例[，以用於 Azure Active Directory B2C](javascript-samples.md)。
