---
title: 建立可呼叫 web Api 的 daemon 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ffbad5981f29ade9f27a434a9273969af4ed82de
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773415"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>案例：呼叫 web Api 的 Daemon 應用程式

瞭解您所需的一切，以建立會呼叫 web Api 的 daemon 應用程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>概觀

您的應用程式可以取得權杖，以代表本身（而非代表使用者）呼叫 Web API。 此案例適用于 daemon 應用程式。 它會使用標準 OAuth 2.0[用戶端認證](v2-oauth2-client-creds-grant-flow.md)授與。

![精靈應用程式](./media/scenario-daemon-app/daemon-app.svg)

以下是一些適用于 daemon 應用程式的使用案例範例：

- 用來布建或管理使用者或在目錄中執行批次處理的 Web 應用程式
- 執行批次作業的桌面應用程式（例如 Windows 上的 Windows 服務或 Linux 上的 daemon 進程），或在背景執行的作業系統服務
- 需要操作目錄，而不是特定使用者的 Web Api

另一個常見的情況是，非 daemon 應用程式會使用用戶端認證：即使是代表使用者採取行動，他們還是必須基於技術的考慮，存取自己的身分識別 Web API 或資源。 例如，存取 Azure Key Vault 中的密碼或快取的 Azure SQL 資料庫。

針對自己的身分識別取得權杖的應用程式：

- 是機密用戶端應用程式。 這些應用程式可讓他們獨立于使用者之外存取資源，因此必須證明其身分識別。 它們也是敏感性應用程式。 他們必須由 Azure Active Directory （Azure AD）租使用者系統管理員核准。
- 已註冊具有 Azure AD 的密碼（應用程式密碼或憑證）。 此密碼會在呼叫 Azure AD 時傳入，以取得權杖。

## <a name="specifics"></a>瞭解

> [!IMPORTANT]
>
> - 使用者無法與 daemon 應用程式互動。 Daemon 應用程式需要自己的身分識別。 這種類型的應用程式會使用其應用程式識別來要求存取權杖，並將其應用程式識別碼、認證（密碼或憑證）和應用程式識別碼 URI 呈現給 Azure AD。 成功驗證之後，背景程式會從 Microsoft 身分識別平臺端點接收存取權杖（和重新整理權杖）。 接著會使用此權杖來呼叫 Web API （並視需要重新整理）。
> - 因為使用者無法與 daemon 應用程式互動，所以不可能進行累加式同意。 所有必要的 API 許可權都必須在應用程式註冊時設定。 應用程式的程式碼只會要求靜態定義的許可權。 這也表示 daemon 應用程式不支援累加式同意。

對於開發人員來說，此案例的端對端體驗具有下列層面：

- Daemon 應用程式只能在 Azure AD 租使用者中使用。 建立一個會嘗試操作 Microsoft 個人帳戶的背景工作應用程式並不合理。 如果您是企業營運（LOB）應用程式開發人員，您會在您的租使用者中建立您的 daemon 應用程式。 如果您是 ISV，您可能會想要建立多租使用者背景工作進程應用程式。 每個租使用者系統管理員都必須提供同意。
- 在[應用程式註冊](./scenario-daemon-app-registration.md)期間，不需要回復 URI。 您需要與 Azure AD 共用秘密或憑證或已簽署的判斷提示。 您也需要要求應用程式許可權，並授與系統管理員同意使用這些應用程式許可權。
- [應用程式](./scenario-daemon-app-configuration.md)設定必須提供用戶端認證，以便在應用程式註冊期間與 Azure AD 共用。
- 用來取得具有用戶端認證流程之權杖的[範圍](scenario-daemon-acquire-token.md#scopes-to-request)必須是靜態範圍。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Daemon 應用程式-應用程式註冊](./scenario-daemon-app-registration.md)
