---
title: Microsoft Identity Web 驗證程式庫總覽
titleSuffix: Microsoft identity platform
description: 深入瞭解 Microsoft 身分識別 Web、適用于 ASP.NET Core 應用程式的驗證和授權程式庫，這些應用程式可與 Azure Active Directory、Azure AD B2C 以及 Microsoft Graph 和其他 Web Api 整合。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 77cb3b0c13a6bfe41c6f7a1a5a0f9d7278aea1db
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930147"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity Web 驗證程式庫

Microsoft Identity Web 是一組 ASP.NET Core 程式庫，可簡化將驗證和授權支援新增至 web 應用程式，以及與 Microsoft 身分識別平臺整合的 web Api。 它提供單一介面 API 便利層，將 ASP.NET Core、其驗證中介軟體，以及 [適用于 .net 的 Microsoft 驗證程式庫 (MSAL) ](https://github.com/azuread/microsoft-authentication-library-for-dotnet)。

## <a name="supported-application-scenarios"></a>支援的應用程式案例

如果您要建立 ASP.NET Core 的 web 應用程式或 web Api，並想要使用 Azure Active Directory (Azure AD) 或 Azure AD B2C 以進行身分識別和存取管理 (IAM) ，建議您在所有這些情況下使用 Microsoft Identity Web：

- [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)
- [代表使用者登入並呼叫 web API 的 web 應用程式](scenario-web-app-call-api-overview.md)
- [只有經過驗證的使用者可以存取的受保護 web API](scenario-protected-web-api-overview.md)
- [受保護的 web API，會代表已登入的使用者呼叫另一個 (下游) web API](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>取得程式庫

您可以從 [NuGet](#nuget)、 [.net Core 專案範本](#project-templates)和 [GitHub](#github)取得 Microsoft 身分識別網站。

#### <a name="nuget"></a>NuGet

Microsoft 身分識別 Web 在 NuGet 上提供為一組套件，可根據您的應用程式需求提供模組化功能。 使用 .NET CLI 的 `dotnet add` 命令或 Visual Studio 的 **NuGet 封裝管理員** 來安裝適用于您專案的套件：

- [Web.config](https://www.nuget.org/packages/Microsoft.Identity.Web) -主要套件。 所有使用 Microsoft 身分識別 Web 的應用程式都需要。
- 。 [UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) -選用。 新增使用者登入和登出的 UI，以及 web 應用程式的相關聯控制器。
- [>microsoft.azure.webjobs.extensions.microsoftgraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -選擇性。 提供與 Microsoft Graph API 的簡化互動。
- [MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) -選擇性。 提供與 Microsoft Graph API [Beta 端點](/graph/api/overview?view=graph-rest-beta&preserve-view=true)的簡化互動。

#### <a name="project-templates"></a>專案範本

Microsoft 身分識別 Web 專案範本包含在 .NET 5.0 中，並且可供 ASP.NET Core 3.1 專案下載。

如果您使用的是 ASP.NET Core 3.1，請使用 .NET CLI 安裝範本：

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

下圖顯示支援的應用程式類型和其相關引數的高階觀點：

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="適用于 Microsoft Identity Web 的可用點 net CLI 專案範本圖表&quot;:::
<br /><sup><b>*</b></sup>`MultiOrg`不支援 `webapi2` ，但可以在*appsettings.js*中啟用，方法是將租使用者設定為 `common` 或`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`Azure AD B2C 不支援

此範例 .NET CLI 命令取自我們的 [Blazor Server 教學](tutorial-blazor-server.md)課程，會產生新的 Blazor 伺服器專案，其中包含正確的套件和起始程式碼 (顯示) 的預留位置值：

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id &quot;00000000-0000-0000-0000-000000000000&quot; --tenant-id &quot;11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web 是裝載在 GitHub 上的開放原始碼專案： <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/microsoft-Identity- <span class="docon docon-navigate-external x-hidden-focus"></span> Web</a>

存放 [庫 wiki](https://github.com/AzureAD/microsoft-identity-web/wiki) 包含其他檔，如果您需要協助或發現錯誤，您可以提出 [問題](https://github.com/AzureAD/microsoft-identity-web/issues)。

## <a name="features"></a>特性

如果您使用預設的 ASP.NET 3.1 專案範本，Microsoft Identity Web 包含數個未提供的功能。

| 功能                                                                                  | ASP.NET Core 3。1                                                     | Microsoft 身分識別 Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 在 web 應用程式中登[入使用者](scenario-web-app-sign-user-app-configuration.md)             | <li>工作或學校帳戶<li>社交身分識別 (與 Azure AD B2C)  | <li>工作或學校帳戶<li>個人 Microsoft 帳戶<li>社交身分識別 (與 Azure AD B2C)      |
| [保護 web Api](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>工作或學校帳戶<li>社交身分識別 (與 Azure AD B2C)  | <li>工作或學校帳戶<li>個人 Microsoft 帳戶<li>社交身分識別 (與 Azure AD B2C)      |
| 多租使用者應用程式中的簽發者驗證                                                   | 否                                                                   | 是，適用于 [所有](authentication-national-cloud.md) 雲端和 [Azure AD B2C](/azure/active-directory-b2c) |
| Web 應用程式/API [呼叫 Microsoft graph] [案例-API-呼叫-graph]                             | 否                                                                   | 是                                                                                                     |
| Web 應用程式/API [呼叫 web API] [案例-api-呼叫-api]                                       | 否                                                                   | 是                                                                                                     |
| 支援憑證認證                                                         | 否                                                                   | 是，包括 Azure Key Vault                                                                          |
| Web apps 中的累加式同意和條件式存取支援                           | 否                                                                   | 是，MVC、Razor pages 和 Blazor                                                                    |
| Web Api 中的權杖加密憑證                                                | 否                                                                   | 是                                                                                                     |
| [範圍/應用程式角色驗證][案例-api-驗證] web Api                        | 否                                                                   | 是                                                                                                     |
| `WWW-Authenticate` web Api 中的標頭產生                                         | 否                                                                   | 是                                                                                                     |

## <a name="next-steps"></a>後續步驟

若要查看 Microsoft 身分識別 Web 的運作方式，請嘗試我們的 Blazor Server 教學課程：

[教學課程：建立使用 Microsoft 身分識別平臺進行驗證的 Blazor 伺服器應用程式](tutorial-blazor-server.md)

GitHub 上的 Microsoft 身分識別 Web wiki 包含程式庫各個層面的廣泛參考檔集。 例如，您可以在這裡找到憑證使用方式、累加式同意和條件式存取參考：

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">使用憑證搭配 web.config <span class="docon docon-navigate-external x-hidden-focus"></span> </a> (GitHub) 
- 累加<a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">式同意和條件 <span class="docon docon-navigate-external x-hidden-focus"></span> 式存取</a> (GitHub) 

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[案例-api-呼叫-api]：案例-web-api-呼叫-api。 md # 選項-1----------sdk [案例------------------------------------------------------------sdk
