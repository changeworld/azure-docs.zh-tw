---
title: 註冊會呼叫 web Api 的 daemon 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式-應用程式註冊
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885475"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>呼叫 web Api 的 Daemon 應用程式-應用程式註冊

若為 daemon 應用程式，以下是註冊應用程式時所需知道的事項。

## <a name="supported-account-types"></a>支援的帳戶類型

Daemon 應用程式只在 Azure AD 租使用者中才有意義。 因此，當您建立應用程式時，您需要選擇下列其中一個選項：

- **僅限此組織目錄中的帳戶**。 這是最常見的選擇，因為背景工作應用程式通常是由企業營運（LOB）開發人員所撰寫。
- **任何組織目錄中的帳戶**。 如果您是為客戶提供公用程式工具的 ISV，則會進行這種選擇。 您需要客戶的租使用者系統管理員核准它。

## <a name="authentication---no-reply-uri-needed"></a>驗證-不需要任何回復 URI

在您的機密用戶端應用程式*只*使用用戶端認證流程的情況下，不需要註冊回復 URI。 應用程式設定或結構不需要此設定。 用戶端認證流程不會使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 許可權-應用程式許可權和系統管理員同意

Daemon 應用程式只能要求 Api 的應用程式許可權（不是委派的許可權）。 在應用程式註冊的 [ **API 許可權**] 頁面上，選取 [**新增許可權**] 並選擇 [API 系列] 之後，選擇 [**應用程式許可權**]，然後選取您的許可權。

![應用程式許可權和系統管理員同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 您想要呼叫的 Web API 必須定義*應用程式許可權（應用程式角色）*，而不是委派的許可權。 如需如何公開這類 API 的詳細資訊，請參閱[受保護的 Web API：應用程式註冊-當您的 Web API 由背景工作應用程式呼叫時](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)。

Daemon 應用程式要求租使用者管理員必須預先同意呼叫 Web API 的應用程式。 租使用者系統管理員選取 [授與系統**管理員同意給*我們的組織*** ]，在相同的**API 許可權**頁面上提供這項同意

如果您是建立多租使用者應用程式的 ISV，您應該閱讀部署多租使用者背景程式應用程式的[部署案例](scenario-daemon-production.md#deployment---multitenant-daemon-apps)一節。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Daemon 應用程式-應用程式代碼設定](./scenario-daemon-app-configuration.md)
