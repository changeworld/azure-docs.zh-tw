---
title: 具現化公共用戶端應用 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 .NET 的 Microsoft 身份驗證庫 （MSAL.NET） 使用配置選項具現化公共用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083588"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>使用配置選項具現化公共用戶端應用程式，使用MSAL.NET

本文介紹如何使用 Microsoft 身份驗證庫對 .NET （MSAL.NET） 具現化[公共用戶端應用程式](msal-client-applications.md)。  應用程式使用設置檔中定義的配置選項進行具現化。

在初始化應用程式之前，首先需要[註冊](quickstart-register-app.md)它，以便應用可以與 Microsoft 標識平臺集成。 註冊後，您可能需要以下資訊（可在 Azure 門戶中找到）：

- 用戶端 ID（表示 GUID 的字串）
- 標識提供程式 URL（名為實例）和應用程式的登錄訪問群體。 這兩個參數統稱為許可權。
- 如果您只為您的組織（也稱為單租戶應用程式）編寫業務線應用程式，則租戶 ID。
- 對於 Web 應用，有時對於公共用戶端應用（尤其是在應用需要使用代理時），您還將設置重定向 Uri，標識提供程式將在其中使用安全權杖聯繫回應用程式。


.NET 核心主控台應用程式可以具有以下*應用程式設定.json*設定檔：

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

以下代碼使用 .NET 組態架構讀取此檔：

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

以下代碼使用設置檔中的配置創建應用程式：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

