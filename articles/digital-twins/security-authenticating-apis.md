---
title: 瞭解 API 身份驗證 - Azure 數位孿生 |微軟文檔
description: 瞭解如何使用 Azure 數位孿生連接到 API 並進行身份驗證。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512992"
---
# <a name="connect-to-and-authenticate-with-apis"></a>使用 API 連接到並進行身份驗證

Azure Digital Twins 會使用 Azure Active Directory (Azure AD) 來驗證使用者並保護應用程式。 Azure AD 支援各種現代架構的驗證。 全部都以業界標準通訊協定 OAuth 2.0 或 OpenID Connect 為基礎。 此外，開發人員可以使用 Azure AD來建置單一租用戶和企業營運 (LOB) 應用程式。 開發人員還可以使用 Azure AD 開發[多租戶應用程式](how-to-multitenant-applications.md)。

如需 Azure AD 的概觀，請造訪[基本概念頁面](https://docs.microsoft.com/azure/active-directory/fundamentals/)，以取得逐步指南、概念及快速入門。

> [!TIP]
> 按照[教程](tutorial-facilities-setup.md)設置並運行 Azure 數位孿生示例應用。

若要整合應用程式或服務與 Azure AD，開發人員必須先向 Azure AD 註冊應用程式。 有關詳細說明和螢幕截圖，請閱讀[此快速入門](../active-directory/develop/quickstart-register-app.md)。

Azure AD 支援[五個主要應用程式案例](../active-directory/develop/v2-app-types.md)：

* 單頁應用程式 (SPA)：使用者必須登入 Azure AD 所保護的單頁應用程式。
* 網頁瀏覽器到 Web 應用程式：使用者必須登入 Azure AD 所保護的 Web 應用程式。
* 原生應用程式到 Web API：在手機、平板電腦或電腦上執行的原生應用程式必須驗證使用者，才能從 Azure AD 所保護的 Web API 取得資源。
* Web 應用程式到 Web API：Web 應用程式必須從 Azure AD 所保護的 Web API 取得資源。
* 精靈或伺服器應用程式到 Web API：無 Web UI 的精靈應用程式或伺服器應用程式必須從 Azure AD 所保護的 Web API 取得資源。

> [!IMPORTANT]
> Azure 數位孿生支援以下兩個身份驗證庫：
> * 較新的[微軟身份驗證庫 （MSAL）](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure 活動目錄身份驗證庫 （ADAL）](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>從中介層 Web API 呼叫 Digital Twins

當開發人員打造 Digital Twins 解決方案時，他們通常會建立中介層應用程式或 API。 應用程式或 API 接著會呼叫 Digital Twins API 下游。 若要支援此標準 Web 解決方案架構，請確定該使用者先：

1. 向中介層應用程式驗證

1. 在驗證期間會取得 OAuth 2.0 代理者權杖

1. 所取得的權杖接著用於進行驗證，或使用代理者流程呼叫更下游的 API

有關如何協調以流量為代表的說明，請閱讀[OAuth 2.0 代表流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 您也可以在[呼叫下游 Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) 中檢視程式碼範例。

## <a name="next-steps"></a>後續步驟

若要使用 OAuth 2.0 隱含授權流程設定及測試 Azure Digital Twins，請閱讀[設定 Postman](./how-to-configure-postman.md)。

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。
