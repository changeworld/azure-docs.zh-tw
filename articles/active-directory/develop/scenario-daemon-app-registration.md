---
title: 註冊調用 Web API 的守護進程應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構 Web API 的守護程式應用程式 - 應用程式註冊
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885475"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>呼叫 Web API 的守護程式應用 - 應用程式註冊

對於守護程序應用程式,請在註冊應用程式時瞭解以下內容。

## <a name="supported-account-types"></a>支援的帳戶類型

守護程序應用程式僅在 Azure AD 租戶中有意義。 因此,在建立應用程式時,您需要選擇以下選項之一:

- **只在此組織目錄中的帳號**。 此選擇是最常見的選擇,因為守護程序應用程式通常由業務線 (LOB) 開發人員編寫。
- **任何組織目錄中的帳號**。 如果您是一個 ISV,為客戶提供實用工具,您將做出此選擇。 您需要客戶的租戶管理員批准它。

## <a name="authentication---no-reply-uri-needed"></a>認證 ─ 沒有回覆的 URI

如果您的機密客戶端應用程式*僅*使用用戶端憑據流,則不需要註冊答覆 URI。 應用程式配置或建構不需要它。 用戶端憑據流不使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 權限 - 應用程式權限和管理員同意

守護程序應用程式只能向 API 請求應用程式許可權(不是委派的許可權)。 在應用程式註冊的**API 許可權**頁上,選擇 **「添加權限」** 並選擇 API 系列後,選擇 **「應用程式許可權**」,然後選擇許可權。

![套用權限與管理員同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 要呼叫的 Web API 需要定義*應用程式權限(應用角色),* 而不是委派許可權。 有關如何公開此類 API 的詳細資訊,請參閱受保護的[Web API:應用程式註冊 - 當守護行程應用呼叫 Web API 時](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)。

守護程序應用程式要求租戶管理員預先同意調用 Web API 的應用程式。 租戶管理員通過選擇 **「授予管理員同意*我們的組織***」,在同一**API 許可權**頁上提供此同意

如果您是建構多租戶應用程式的 ISV,則應閱讀[多租戶守護程序應用的「部署」部分](scenario-daemon-production.md#deployment---multitenant-daemon-apps)。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [守護程式應用程式 - 應用程式代碼設定](./scenario-daemon-app-configuration.md)
