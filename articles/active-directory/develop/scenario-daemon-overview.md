---
title: 建立可呼叫 web Api 的 daemon 應用程式 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 45af940382a261f1793583e471871b9cceed0a57
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753824"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>案例：呼叫 Web API 的精靈應用程式

瞭解建立可呼叫 web Api 的背景程式應用程式所需的一切。

## <a name="overview"></a>概觀

您的應用程式可以取得權杖來代表本身呼叫 web API (而不是代表使用者) 。 此案例適用于 daemon 應用程式。 其會使用標準 OAuth 2.0 [用戶端認證](v2-oauth2-client-creds-grant-flow.md)授與。

![精靈應用程式](./media/scenario-daemon-app/daemon-app.svg)

以下是一些適用于 daemon 應用程式的使用案例範例：

- 用來布建或管理使用者或在目錄中進行批次處理的 Web 應用程式
- 桌面應用程式 (像是 Windows 上的 Windows 服務或 Linux) 上執行 batch 作業的 daemon 進程，或在背景執行的作業系統服務
- 需要操作目錄，而不是特定使用者的 Web Api

另一個常見的情況是，非背景程式應用程式會使用用戶端認證：即使它們代表使用者執行動作，他們仍需要在其本身的身分識別下存取 web API 或資源，以確保技術的原因。 例如，存取 Azure Key Vault 中的密碼或快取的 Azure SQL Database。

針對自己的身分識別取得權杖的應用程式：

- 是機密用戶端應用程式。 這些應用程式（假設它們可獨立于使用者存取資源）需要證明其身分識別。 它們也是敏感性應用程式。 它們必須由 Azure Active Directory (Azure AD) 租使用者系統管理員核准。
- 已向 Azure AD 註冊 (應用程式密碼或憑證) 的秘密。 此密碼會在呼叫 Azure AD 期間傳入，以取得權杖。

## <a name="specifics"></a>特性

> [!IMPORTANT]
>
> - 使用者無法與 daemon 應用程式互動。 Daemon 應用程式需要自己的身分識別。 這種類型的應用程式會使用其應用程式識別來要求存取權杖，並將其應用程式識別碼、認證 (密碼或憑證) ，以及將應用程式識別碼 URI 呈現給 Azure AD。 成功驗證之後，背景程式會收到 (的存取權杖，以及從 Microsoft 身分識別平臺) 的重新整理權杖。 然後，此權杖會用來呼叫 web API (，並視需要重新整理) 。
> - 因為使用者無法與背景程式應用程式互動，所以不可能進行累加式同意。 所有必要的 API 許可權都必須在應用程式註冊時進行設定。 應用程式的程式碼只會要求靜態定義的許可權。 這也表示 daemon 應用程式不支援累加式同意。

針對開發人員，此案例的端對端體驗具有下列層面：

- Daemon 應用程式只能在 Azure AD 租使用者中運作。 建立可嘗試操作 Microsoft 個人帳戶的背景程式應用程式並不合理。 如果您是企業營運 (LOB) 應用程式開發人員，您將在您的租使用者中建立您的 daemon 應用程式。 如果您是 ISV，您可能會想要建立多租使用者背景工作應用程式。 每個租使用者系統管理員都必須提供同意。
- 在 [應用程式註冊](./scenario-daemon-app-registration.md)期間，不需要回復 URI。 您需要與 Azure AD 共用秘密或憑證或簽署的判斷提示。 您也需要要求應用程式許可權，並授與系統管理員同意才能使用這些應用程式許可權。
- [應用](./scenario-daemon-app-configuration.md)程式設定必須在應用程式註冊期間提供與 Azure AD 共用的用戶端認證。
- 使用用戶端認證流程取得權杖的 [範圍](scenario-daemon-acquire-token.md#scopes-to-request) 必須是靜態範圍。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

請移至此案例的 [應用程式註冊](./scenario-daemon-app-registration.md)中的下一篇文章。
