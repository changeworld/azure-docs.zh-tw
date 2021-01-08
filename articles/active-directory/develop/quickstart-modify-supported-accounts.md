---
title: 操作說明：變更應用程式所支援的帳戶類型 | Azure
titleSuffix: Microsoft identity platform
description: 在本操作說明中，您會設定向 Microsoft 身分識別平台註冊的應用程式，以變更可以存取應用程式的使用者或帳戶。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 1d30d1e6c9cc60af96336cfdc7bea7126b4df7a8
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017624"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>如何修改應用程式所支援的帳戶

向 Microsoft 身分識別平台註冊應用程式時，同時指定了誰 (哪些帳戶類型) 可以存取該應用程式。 例如，您可能已在組織中指定帳戶，這是「單一租用戶」應用程式。 或者，您可能已在任何組織 (包括您的) 中指定帳戶，這是「多租用戶」應用程式。

在下列章節中，您會了解如何在 Azure 入口網站中修改您的應用程式註冊，以變更可以存取應用程式的使用者或帳戶類型。

## <a name="prerequisites"></a>Prerequisites

* [在您的 Azure AD 租用戶中註冊的應用程式](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>變更應用程式註冊以支援不同的帳戶

若要針對現有應用程式註冊所支援的帳戶類型指定不同的設定：

1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [管理]下選取 [應用程式註冊]，再選取您的應用程式。
1. 現在指定可以使用應用程式的人員，有時也稱為「登入受眾」。

    | 支援的帳戶類型 | 描述 |
    |-------------------------|-------------|
    | **僅此組織目錄中的帳戶** | 如果您要建置的應用程式僅供「您」租用戶中的使用者 (或來賓) 使用，請選取此選項。<br><br>通常稱為「企業營運應用程式」 (LOB) ，這是 Microsoft 身分識別平台中的 **單一租用戶** 應用程式。 |
    | **任何組織目錄中的帳戶** | 如果您想要讓「任何」Azure AD 租用戶中的使用者都能使用您的應用程式，請選取此選項。 例如，如果要建立要提供給多個組織的軟體即服務 (SaaS) 應用程式，則適合使用此選項。<br><br>這在 Microsoft 身分識別平台中稱為 **多租用戶** 應用程式。 |
1. 選取 [儲存]。

### <a name="why-changing-to-multi-tenant-can-fail"></a>無法變更為多租用戶的原因

因為應用程式識別碼 URI (應用程式識別碼 URI) 名稱發生衝突，所以將應用程式註冊從單一切換到多租用戶有時會失敗。 範例應用程式識別碼 URI 為 `https://contoso.onmicrosoft.com/myapp`。

「應用程式識別碼 URI」是其中一種可在通訊協定訊息中識別應用程式的方式。 在單一租用戶應用程式中，只要該租用戶內有唯一的應用程式識別碼 URI 即可。 就多租用戶應用程式而言，該 URI 則必須具全域唯一性，Azure AD 才能在所有租用戶中找到該應用程式。 系統會透過要求「應用程式識別碼 URI」主機名稱必須與其中一個 Azure AD 租用戶的[驗證發行者網域](howto-configure-publisher-domain.md)相符，來強制執行全域唯一性。

例如，如果租用戶的名稱是 contoso.onmicrosoft.com，則有效的應用程式識別碼 URI 會是 `https://contoso.onmicrosoft.com/myapp`。 如果租用戶的已驗證網域是 contoso.com，則有效的應用程式識別碼 URI 也會是 `https://contoso.com/myapp`。 如果應用程式識別碼 URI 未遵循第二個模式，`https://contoso.com/myapp`，則系統無法將應用程式註冊轉換成多租用戶。

如需設定已驗證之發行者網域的詳細資訊，請參閱[設定已驗證的網域](howto-configure-publisher-domain.md)。

## <a name="next-steps"></a>後續步驟

深入了解[將應用程式從單一租用戶轉換為多租用戶](howto-convert-app-to-be-multi-tenant.md)的需求。
