---
title: 建立新的 Azure Application Insights 資源 | Microsoft Docs
description: 針對新的即時應用程式手動設定 Application Insights 監視。
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c1b3a6920723ad59b714cce4bd69e1b95fe1995f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132412"
---
# <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

Azure Application Insights 會在 Microsoft Azure「資源」** 中顯示您應用程式的相關資料。 因此，建立新的資源是屬於[設定 Application Insights 以監視新應用程式][start]的一環。 創建新資源後，可以獲取其檢測金鑰，並用它來配置應用程式見解 SDK。 檢測金鑰將遙測資料連結到資源。

## <a name="sign-in-to-microsoft-azure"></a>登入 Microsoft Azure

如果沒有 Azure 訂閱，請先創建[一個免費](https://azure.microsoft.com/free/)帳戶。"

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

登錄到 Azure[門戶](https://portal.azure.com)，並創建應用程式見解資源：

![按一下左上角的"+"符號。 選擇開發人員工具後跟應用程式見解](./media/create-new-resource/new-app-insights.png)

   | 設定        |  值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 唯一值 | 標識要監視的應用的名稱。 |
   | **資源組**     | myResourceGroup      | 用於承載應用見解資料的新資源組或現有資源組的名稱。 |
   | **位置** | 美國東部 | 選擇您附近的位置，或靠近您的應用託管地。 |

> [!NOTE]
> 雖然您可以在不同的資源組中使用相同的資源名稱，但使用全域唯一名稱可能是有益的。 如果您計畫[執行跨資源查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application)，因為它簡化了所需的語法，則此功能非常有用。

在所需欄位中輸入適當的值，然後選擇 **"審閱 + 創建**"。

![在必要欄位中輸入值，然後選擇"審核 + 創建"。](./media/create-new-resource/review-create.png)

創建應用後，將打開一個新窗格。 此窗格是查看有關受監視應用程式的性能和使用方式資料的位置。 

## <a name="copy-the-instrumentation-key"></a>複製檢測金鑰

檢測鍵標識要將遙測資料與的資源相關聯。 您需要複製檢測金鑰並將其添加到應用程式的代碼。

![按一下並複製檢測金鑰](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>在應用程式中安裝 SDK

在應用程式中安裝 Application Insights SDK 核心。 此步驟高度仰賴於應用程式的類型。

使用檢測金鑰來設定[您在應用程式中安裝的 SDK][start]。

SDK 包括標準模組，這些模組無需編寫任何其他代碼即可發送遙測資料。 若要更詳細追蹤使用者動作或診斷問題，請[使用 API][api] 來傳送您自己的遙測。

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

有關此 Cmdlet 的完整 PowerShell 文檔，以及如何檢索檢測金鑰，請參閱 Azure [PowerShell 文檔](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)。

### <a name="azure-cli-preview"></a>Azure CLI（預覽版）

要訪問預覽應用程式見解 Azure CLI 命令，您首先需要運行：

```azurecli
 az extension add -n application-insights
```

如果不運行該命令，`az extension add`您將看到一條錯誤訊息，指出：`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

現在，您可以運行以下內容來創建應用程式見解資源：

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

有關此命令的完整 Azure CLI 文檔，並瞭解如何檢索檢測金鑰，請參閱 Azure [CLI 文檔](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)。

## <a name="next-steps"></a>後續步驟
* [診斷搜索](../../azure-monitor/app/diagnostic-search.md)
* [探索度量](../../azure-monitor/app/metrics-explorer.md)
* [撰寫分析查詢](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md
