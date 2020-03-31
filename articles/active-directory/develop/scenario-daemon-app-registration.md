---
title: 註冊調用 Web API 的守護進程應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的守護進程應用 - 應用註冊
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
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773375"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>調用 Web API 的守護程式應用 - 應用註冊

對於守護進程應用程式，請在註冊應用程式時瞭解以下內容。

## <a name="supported-account-types"></a>支援的帳戶類型

守護進程應用程式僅在 Azure AD 租戶中有意義。 因此，在創建應用程式時，您需要選擇以下選項之一：

- **僅此組織目錄中的帳戶**。 此選擇是最常見的選擇，因為守護進程應用程式通常由業務線 （LOB） 開發人員編寫。
- **任何組織目錄中的帳戶**。 如果您是一個 ISV，為客戶提供公用程式，您將做出此選擇。 您需要客戶的租戶管理員批准它。

## <a name="authentication---no-reply-uri-needed"></a>身份驗證 - 無需回復 URI

如果您的機密用戶端應用程式*僅*使用用戶端憑據流，則不需要註冊答覆 URI。 應用程式佈建或構造不需要它。 用戶端憑據流不使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 許可權 - 應用許可權和管理員同意

守護進程應用程式只能向 API 請求應用程式許可權（不是委派的許可權）。 在應用程式註冊的**API 許可權**頁上，選擇 **"添加許可權"** 並選擇 API 系列後，選擇 **"應用程式許可權**"，然後選擇許可權。

![應用許可權和管理員同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 要調用的 Web API 需要定義*應用程式許可權（應用角色），* 而不是委派許可權。 有關如何公開此類 API 的詳細資訊，請參閱受保護的[Web API：應用註冊 - 當守護進程應用調用 Web API 時](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)。

守護進程應用程式要求租戶管理員預先同意調用 Web API 的應用程式。 租戶管理員通過選擇 **"授予管理員同意*我們的組織***"，在同一**API 許可權**頁上提供此同意

如果您是構建多租戶應用程式的 ISV，則應閱讀[多租戶守護進程應用的"部署"部分](scenario-daemon-production.md#deployment---multitenant-daemon-apps)。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [守護進程應用 - 應用代碼配置](./scenario-daemon-app-configuration.md)
