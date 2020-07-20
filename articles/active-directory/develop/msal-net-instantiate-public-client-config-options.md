---
title: 具現化公用用戶端應用程式（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET），以設定選項具現化公用用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 171b4ddf9a6f3cb5868f2300ea892ad785cbaf22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477425"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>使用 MSAL.NET 以設定選項具現化公用用戶端應用程式

本文說明如何使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）來具現化[公用用戶端應用程式](msal-client-applications.md)。  應用程式會使用設定檔中定義的設定選項來具現化。

在初始化應用程式之前，您必須先[註冊](quickstart-register-app.md)它，您的應用程式才能與 Microsoft 身分識別平臺整合。 註冊之後，您可能需要下列資訊（可在 Azure 入口網站中找到）：

- 用戶端識別碼（代表 GUID 的字串）
- 應用程式的身分識別提供者 URL （名為實例）和登入物件。 這兩個參數統稱為授權單位。
- 租使用者識別碼，如果您要撰寫僅供組織使用的企業營運應用程式（也稱為單一租使用者應用程式）。
- 對於 web 應用程式，有時也適用于公用用戶端應用程式（特別是當您的應用程式需要使用訊息代理程式時），您也會設定 redirectUri，其中身分識別提供者會使用安全性權杖來與您的應用程式連線。


.NET Core 主控台應用程式可能會在設定檔上具有下列*appsettings.js* ：

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

下列程式碼會使用 .NET 設定架構來讀取此檔案：

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

下列程式碼會使用設定檔中的設定來建立您的應用程式：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

