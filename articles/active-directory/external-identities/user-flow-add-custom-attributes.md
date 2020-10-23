---
title: 將自訂屬性新增至自助式註冊流程-Azure AD
description: 了解如何為您的自助式註冊使用者流程自訂屬性。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e64ab70fed13d4ca907b2bfb3aa448acdedc39e9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441448"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>定義使用者流程的自訂屬性 (預覽)

> [!NOTE]
> 自訂使用者屬性功能是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

對於各個應用程式，您要在註冊期間收集的資訊可能會有不同的需求。 Azure AD 隨附一組儲存在屬性中的內建資訊，例如名字、姓氏、城市及郵遞區號。 使用 Azure AD，您可以擴充在外部使用者透過使用者流程註冊時儲存在來賓帳戶上的屬性集。

您可以在 Azure 入口網站中建立自訂屬性，並在您的自助式註冊使用者流程中加以使用。 您也可以使用 [Microsoft Graph API](../../active-directory-b2c/manage-user-accounts-graph-api.md) 來讀取和寫入這些屬性。 Microsoft Graph API 支援以擴充屬性建立和更新使用者。 圖形 API 中的擴充屬性會使用 `extension_<extensions-app-id>_attributename` 慣例來命名。 例如：

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<extensions-app-id>`是租使用者專用的。 若要尋找此識別碼，請流覽至 Azure Active Directory > 應用程式註冊 > 所有應用程式。 搜尋開頭為「aad-擴充功能-應用程式」的應用程式，並加以選取。 在應用程式的 [總覽] 頁面上，記下應用程式 (用戶端) 識別碼。

## <a name="create-a-custom-attribute"></a>建立自訂屬性

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [自訂使用者屬性 (預覽)]。 列出可用的使用者屬性。

   ![選取註冊的使用者屬性](media/user-flow-add-custom-attributes/user-attributes.png)

5. 若要新增屬性，請選取 [新增]。
6. 在 [新增屬性] 窗格中，輸入下列值：

   - **名稱** - 提供自訂屬性的名稱 (例如 "Shoesize")。
   - **資料類型** - 選擇資料類型 (**字串**、**布林值**或 **Int**)。
   - **描述** - 選擇性地輸入自訂屬性的描述，以供內部使用。 使用者看不到此描述。

   ![新增屬性](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. 選取 [建立]。

使用者屬性清單現已提供自訂屬性，且您可將其用於使用者流程中。 只有在第一次用於任何使用者流程時才會建立自訂屬性，而不是在您將其新增至使用者屬性清單時建立。

在您使用新建立的自訂屬性建立新的使用者後，即可在 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)中查詢物件。 您現在應該會在使用者物件上的註冊旅程期間所收集的屬性清單中看到 **ShoeSize** 。 您可以從應用程式呼叫圖形 API，以便在將資料加入至使用者物件之後，從這個屬性取得資料。

## <a name="next-steps"></a>後續步驟

[將自助式註冊使用者流程新增至應用程式](self-service-sign-up-user-flow.md)