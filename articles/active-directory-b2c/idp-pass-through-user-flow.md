---
title: 通過使用者流將訪問權杖傳遞到應用
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure 活動目錄 B2C 中的使用者流中作為聲明傳遞 OAuth 2.0 標識提供程式的訪問權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187774"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>透過使用者流程將存取權杖傳遞到 Azure Active Directory B2C 中的應用程式

Azure 活動目錄 B2C（Azure AD B2C） 中的[使用者流](user-flow-overview.md)為應用程式使用者提供了註冊或登錄標識提供程式的機會。 當發生這種情況時，Azure AD B2C 會從身分識別提供者處收到[存取權杖](tokens-overview.md)。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您在使用者流程中啟用宣告，以將權杖傳遞至您在 Azure AD B2C 中註冊的應用程式。

Azure AD B2C 目前僅支援傳遞 [OAuth 2.0](authorization-code-flow.md) 身分識別提供者的存取權杖，其中包括 [Facebook](identity-provider-facebook.md) 和 [Google](identity-provider-google.md)。 對於所有其他識別提供者，宣告會傳回空白。

## <a name="prerequisites"></a>Prerequisites

* 您的應用程式必須使用 [v2 使用者流程](user-flow-versions.md)。
* 您的自訂原則是使用 OAuth 2.0 識別提供者設定。

## <a name="enable-the-claim"></a>啟用宣告

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
2. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含租戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
4. 選擇**使用者流（策略），** 然後選擇使用者流。 例如 **，B2C_1_signupsignin1**。
5. 選取 [應用程式宣告]****。
6. 啟用**標識提供程式訪問權杖**聲明。

    ![啟用標識提供程式訪問權杖聲明](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. 按一下 [儲存]**** 以儲存使用者流程。

## <a name="test-the-user-flow"></a>測試使用者流程

在 Azure AD B2C 中測試應用程式時，將 Azure AD B2C 權杖傳回到 `https://jwt.ms` 以檢閱其中的宣告很有用。

1. 在使用者流程的 [概觀] 頁面中，選取 [執行使用者流程]****。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看下面範例中的權杖，**回覆 URL** 應該會顯示 `https://jwt.ms`。
3. 按一下 [執行使用者流程]****，然後使用您的帳戶認證登入。 您應該會在 **idp_access_token** 宣告中看到識別提供者的存取權杖。

    您應該會看到類似下列範例的內容：

    ![jwt.ms解碼的權杖，並突出顯示idp_access_token塊](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>後續步驟

在 Azure [AD B2C 權杖概述](tokens-overview.md)中瞭解更多資訊。
