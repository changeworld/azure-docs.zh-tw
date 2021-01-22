---
title: 在 Azure Functions 中啟用適用于 .NET 和 .NET Core 應用程式的快照偵錯工具 |Microsoft Docs
description: 在 Azure Functions 中啟用適用于 .NET 和 .NET Core 應用程式的快照偵錯工具
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: d86455eae0834f29099c7d5c96f8326408daf519
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675524"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>在 Azure Functions 中啟用適用于 .NET 和 .NET Core 應用程式的快照偵錯工具

快照偵錯工具目前適用于在 Windows 服務方案 Azure Functions 上執行的 ASP.NET 和 ASP.NET Core 應用程式。

使用快照偵錯工具時，建議您在基本服務層級或更高版本上執行您的應用程式。

在大部分的應用程式中，免費和共用的服務層級沒有足夠的記憶體或磁碟空間來儲存快照集。

## <a name="prerequisites"></a>必要條件

* [在您的函數應用程式中啟用 Application Insights 監視](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>啟用快照偵錯工具

如果您正在執行不同類型的 Azure 服務，則以下是在其他支援的平臺上啟用快照偵錯工具的指示：
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

若要在您的函式應用程式中啟用快照偵錯工具，您必須 `host.json` 新增如下所 `snapshotConfiguration` 定義的屬性，並重新部署您的函式，以更新您的檔案。

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

快照偵錯工具在 Azure Functions 執行時間中會預先安裝，預設為停用。

由於快照偵錯工具它包含在 Azure Functions 執行時間中，因此不需要新增額外的 NuGet 套件或應用程式設定。

就像參考一樣，若要讓簡單的函式應用程式 ( .NET Core) ，下面是 `.csproj` 在 `{Your}Function.cs` `host.json` 其上啟用快照偵錯工具的方式。

Project .csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function 類別

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

主機檔案

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="disable-snapshot-debugger"></a>停用快照偵錯工具

若要停用函式應用程式中的快照偵錯工具，您只需要將 `host.json` 設定為屬性來更新您 `false` 的檔案 `snapshotConfiguration.isEnabled` 。

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

建議您在所有應用程式上都已啟用快照偵錯工具，以簡化應用程式例外狀況的診斷。

## <a name="next-steps"></a>下一步

- 產生可觸發例外狀況的應用程式流量。 然後，等候10到15分鐘，將快照集傳送至 Application Insights 實例。
- [查看](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) Azure 入口網站中的快照集。
- 根據您的函數應用程式的使用案例自訂快照偵錯工具設定。 如需詳細資訊，請參閱 [host.js中的快照](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration)集設定。
- 如需疑難排解快照偵錯工具問題的協助，請參閱 [快照偵錯工具疑難排解](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。