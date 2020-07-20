---
title: 設定會話行為-Azure Active Directory B2C |Microsoft Docs
description: 在 Azure Active Directory B2C 中設定會話行為。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed10a9258590f8f9037a574bd0322a82dd309a5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385241"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定會話行為

Azure Active Directory B2C （Azure AD B2C）中的[單一登入（SSO）會話](session-overview.md)管理可讓系統管理員在使用者已通過驗證後，控制與使用者的互動。 例如，系統管理員可以控制是否顯示選取的身分識別提供者，或是否需要再次輸入帳戶詳細資料。 本文說明如何設定 Azure AD B2C 的 SSO 設定。

## <a name="session-behavior-properties"></a>會話行為屬性

您可以使用下列屬性管理 Web 應用程式工作階段︰

- **Web 應用程式工作階段存留期 (分鐘)** - 在成功通過驗證時，使用者瀏覽器中所儲存 Azure AD B2C 工作階段 Cookie 的存留期。
    - 預設值 = 1440 分鐘。
    - 最小值 (含) = 15 分鐘。
    - 最大值 (含) = 1440 分鐘。
- **Web 應用程式會話超時**-[會話到期類型](session-overview.md#session-expiry-type)、*回復*或*絕對*。 
- **單一登入**設定-在 Azure AD B2C 租使用者中，跨多個應用程式和使用者流程之單一登入（SSO）行為的[會話範圍](session-overview.md#session-scope)。 


## <a name="configure-the-properties"></a>設定屬性

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [使用者流程 (原則)]。
5. 開啟您先前建立的使用者流程。
6. 選取 [屬性] 。
7. 設定**web 應用程式會話存留期（分鐘）**、 **web 應用程式會話超時**、**單一登入**設定，並視需要**在登出要求中要求識別碼權杖**。

    ![Azure 入口網站中的會話行為屬性設定](./media/session-behavior/session-behavior.png)

8. 按一下 [檔案] 。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure AD B2C 的會話](session-overview.md)。