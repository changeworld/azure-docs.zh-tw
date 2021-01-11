---
title: 具現化公用用戶端應用程式 (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) ，以設定選項具現化公用用戶端應用程式。
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064703"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>使用 MSAL.NET 以設定選項具現化公用用戶端應用程式

本文說明如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 來具現化 [公用用戶端應用程式](msal-client-applications.md) 。  應用程式會使用設定檔中定義的設定選項來具現化。

在初始化應用程式之前，您必須先 [註冊](quickstart-register-app.md) 該應用程式，讓您的應用程式可以與 Microsoft 身分識別平臺整合。 註冊之後，您可能需要下列資訊 (可在 Azure 入口網站) 中找到：

- 用戶端識別碼 (代表 GUID 的字串) 
- 身分識別提供者 URL (名為實例) ，以及您應用程式的登入物件。 這兩個參數統稱為授權單位。
- 租使用者識別碼（如果您只是針對您的組織撰寫企業營運應用程式） (也命名為單一租使用者應用程式) 。
- 針對 web 應用程式，有時適用于公用用戶端應用程式 (特別是當您的應用程式需要使用訊息代理程式) 時，您也會設定 redirectUri，讓身分識別提供者使用安全性權杖來與您的應用程式連線。


.NET Core 主控台應用程式可能會在設定檔上有下列 *appsettings.js* ：

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

下列程式碼會使用設定檔案中的設定來建立您的應用程式：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

