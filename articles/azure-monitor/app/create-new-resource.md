---
title: 建立新的 Azure Application Insights 資源 | Microsoft Docs
description: 針對新的即時應用程式手動設定 Application Insights 監視。
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 090c983dcca101557f8dec479d1267275d24908b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374909"
---
# <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

Azure Application Insights 會在 Microsoft Azure「資源」中顯示您應用程式的相關資料。 因此，建立新的資源是[設定 Application Insights 以監視新應用程式][start]的一部分。 建立新資源之後，您可以取得其檢測金鑰，並使用它來設定 Application Insights SDK。 檢測金鑰會將您的遙測連結至資源。

## <a name="sign-in-to-microsoft-azure"></a>登入以 Microsoft Azure

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

登入[Azure 入口網站](https://portal.azure.com)，並建立 Application Insights 資源：

![按一下左上角的 [+] 符號。 依序選取 開發人員工具 和 Application Insights](./media/create-new-resource/new-app-insights.png)

   | 設定        |  值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 唯一值 | 識別您要監視之應用程式的名稱。 |
   | **資源群組**     | myResourceGroup      | 要裝載 App Insights 資料的新或現有資源群組的名稱。 |
   | **位置** | 美國東部 | 選擇您附近或接近應用程式裝載位置的位置。 |

> [!NOTE]
> 雖然您可以在不同的資源群組上使用相同的資源名稱，但使用全域唯一的名稱可能會很有説明。 如果您打算[執行跨資源查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application)，因為它會簡化必要的語法，這會很有用。

在必要欄位中輸入適當的值，然後選取 [**審查 + 建立**]。

![在 [必要欄位] 中輸入值，然後選取 [審查 + 建立]。](./media/create-new-resource/review-create.png)

當您的應用程式已建立時，新的窗格隨即開啟。 此窗格可讓您查看受監視應用程式的效能和使用量資料。 

## <a name="copy-the-instrumentation-key"></a>複製檢測金鑰

檢測金鑰會識別您想要與遙測資料建立關聯的資源。 您將需要 [複製]，才能將檢測金鑰新增至應用程式的程式碼。

![按一下並複製檢測金鑰](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>在應用程式中安裝 SDK

在應用程式中安裝 Application Insights SDK 核心。 此步驟高度仰賴於應用程式的類型。

使用檢測金鑰來設定[您在應用程式中安裝的 SDK][start]。

SDK 包含可傳送遙測資料的標準模組，而您不需要撰寫任何額外的程式碼。 若要追蹤使用者動作或更詳細地診斷問題，請[使用 API][api]來傳送您自己的遙測。

## <a name="creating-a-resource-automatically"></a>自動建立資源

### <a name="powershell"></a>PowerShell

建立新 Application Insights 資源

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>範例

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>結果

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

如需此 Cmdlet 的完整 PowerShell 檔，並瞭解如何取得檢測金鑰，請參閱[Azure PowerShell 檔](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)。

### <a name="azure-cli-preview"></a>Azure CLI （預覽）

若要存取預覽 Application Insights 您必須先執行的 Azure CLI 命令：

```azurecli
 az extension add -n application-insights
```

如果您未執行 `az extension add` 命令，您會看到一則錯誤訊息，說明： `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

現在您可以執行下列程式來建立您的 Application Insights 資源：

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>範例

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>結果

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

如需此命令的完整 Azure CLI 檔，並瞭解如何取得檢測金鑰，請參閱[Azure CLI 檔](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)。

## <a name="next-steps"></a>後續步驟
* [診斷搜尋](../../azure-monitor/app/diagnostic-search.md)
* [探索度量](../../azure-monitor/app/metrics-explorer.md)
* [撰寫分析查詢](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md
