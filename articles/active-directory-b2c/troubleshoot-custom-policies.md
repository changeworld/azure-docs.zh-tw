---
title: 針對 Azure Active Directory B2C 中的自訂原則進行疑難排解
description: 了解在 Azure Active Directory B2C 中使用自訂原則時，解決錯誤的方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840248"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>針對 Azure AD B2C 自訂原則和身分識別體驗架構進行疑難排解

如果您使用 Azure Active Directory B2C (Azure AD B2C) 自訂原則，當您以原則語言 XML 格式來設定身分識別體驗架構時，您可能會遇到挑戰。 學習撰寫自訂原則就如同學習新的語言。 在本文中，我們將說明一些可協助您找出並解決問題的工具和秘訣。

本文著重在針對 Azure AD B2C 自訂原則設定進行疑難排解。 其中不討論信賴憑證者應用程式或其身分識別程式庫。

## <a name="xml-editing"></a>XML 編輯

設定自訂原則時，最常見的錯誤是 XML 格式不正確。 良好的 XML 編輯器幾乎不可或缺。 它會以原生方式、色彩代碼內容、預先填滿的一般詞彙來顯示 XML，讓 XML 專案保持索引，並且可以針對 XML 架構進行驗證。

其中兩個最愛的編輯器是 [Visual Studio Code](https://code.visualstudio.com/) 和 [Notepad + +](https://notepad-plus-plus.org/)。

在您上傳 XML 檔案之前，XML 結構描述驗證會識別錯誤。 在 [入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)的根資料夾中，取得 TRUSTFRAMEWORKPOLICY_0 的 XML 架構定義檔。 *3.0.0 .xsd*。 若要瞭解如何在編輯器中使用 XSD 架構檔案進行驗證，請在編輯器的檔中尋找 *xml 工具* 和 *xml 驗證* 或類似的內容。

您可能會發現檢閱 XML 規則很有幫助。 Azure AD B2C 會拒絕它所偵測到的任何 XML 格式錯誤。 格式錯誤的 XML 有時可能會產生令人誤解的錯誤訊息。

## <a name="upload-policies-and-policy-validation"></a>上傳原則和原則驗證

XML 原則檔的驗證會在上傳時自動執行。 大部分的錯誤會導致上傳失敗。 驗證包括您要上傳的原則檔。 也包括上傳檔案所參考的一連串檔案 (信賴憑證者原則檔、擴充檔案和基底檔案)。

常見的驗證錯誤包括下列各項：

> 錯誤程式碼片段︰`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* ClaimType 值可能拼字錯誤，或不存在於結構描述中。
* ClaimType 值至少必須定義於原則內的其中一個檔案中。
    例如：`<ClaimType Id="issuerUserId">`
* 如果 ClaimType 定義於擴充檔案中，但也用於基底檔案的 TechnichalProfile 值中，則上傳基底檔案會導致錯誤。

> 錯誤程式碼片段︰`...makes a reference to a ClaimsTransformation with id...`

* 此錯誤的原因可能與 ClaimType 錯誤相同。

> 錯誤程式碼片段︰`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* 檢查和元素中的 TenantId 值 `<TrustFrameworkPolicy\>` 是否 `<BasePolicy\>` 符合您的目標 Azure AD B2C 租使用者。

## <a name="troubleshoot-the-runtime"></a>針對執行階段進行疑難排解

* 使用 [ **立即執行** ] 和 `https://jwt.ms` 個別測試您的 web 或行動應用程式的原則。 此網站的作用就像信賴憑證者應用程式。 它會顯示 Azure AD B2C 原則所產生 (JWT) 的 JSON web 權杖內容。

    若要建立可重新導向至以 `https://jwt.ms` 進行權杖檢查的測試應用程式：

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* 若要追蹤用戶端瀏覽器和 Azure AD B2C 之間的訊息交換，請使用 [Fiddler](https://www.telerik.com/fiddler)。 它可協助您了解使用者旅程圖在協調流程步驟中的何處失敗。

* 在 [開發模式](troubleshoot-with-application-insights.md)中，請使用 **Application Insights** 來追蹤身分識別體驗架構使用者旅程圖的活動。 在 **開發模式** 中，您可以觀察 Identity Experience Framework 之間的宣告交換，以及技術設定檔所定義的各種宣告提供者，例如身分識別提供者、API 型服務、Azure AD B2C 的使用者目錄和其他服務，例如 Azure AD Multi-Factor Authentication。

## <a name="recommended-practices"></a>建議的做法

**保留您案例的多個版本。使用您的應用程式將專案群組在專案中。** 基底、擴充和信賴憑證者檔案直接相互依存。 將它們儲存成一個群組。 有新功能新增至您的原則時，保留個別的工作版本。 將工作版本和其所互動的應用程式程式碼，分階段放入您自己的檔案系統中。 您的應用程式可能會在一個租用戶中叫用許多不同的信賴憑證者原則。 它們可能會變成相依於您的 Azure AD B2C 原則可能提供的宣告。

**使用已知的使用者旅程圖來開發和測試技術設定檔。** 使用已測試的入門套件原則來設定您的技術設定檔。 將它們併入您自己的使用者旅程圖之前，先個別進行測試。

**使用已測試的技術設定檔來開發和測試使用者旅程圖。** 累加地變更使用者旅程圖的協調流程步驟。 漸進地建置您想要的情節。

## <a name="next-steps"></a>後續步驟

在 GitHub 上提供，請下載 [active directory-b2c-自訂原則-active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip 封存。 您也可以複製存放庫：

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
