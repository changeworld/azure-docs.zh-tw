---
title: 建立新的 Azure Application Insights 資源 | Microsoft Docs
description: 針對新的即時應用程式手動設定 Application Insights 監視。
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 2924ccbbf591020d7872d3e9cca62a9c271db7c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944601"
---
# <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

Azure 應用程式 Insights 會在 Microsoft Azure *資源* 中顯示您應用程式的相關資料。 因此，建立新的資源是屬於[設定 Application Insights 以監視新應用程式][start]的一環。 建立新資源之後，您可以取得其檢測金鑰，並使用該金鑰來設定 Application Insights SDK。 檢測金鑰會將您的遙測連結至資源。

## <a name="sign-in-to-microsoft-azure"></a>登入 Microsoft Azure

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

登入 [Azure 入口網站](https://portal.azure.com)，並建立 Application Insights 資源：

![按一下左上角的 [+] 符號。 選取開發人員工具，後面接著 Application Insights](./media/create-new-resource/new-app-insights.png)

   | 設定        |  值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | `Unique value` | 識別您要監視之應用程式的名稱。 |
   | **資源群組**     | `myResourceGroup`      | 用來裝載 App Insights 資料之新的或現有資源群組的名稱。 |
   | **區域** | `East US` | 選擇您附近或接近應用程式裝載位置的地點。 |
   | **資源模式** | `Classic` 或 `Workspace-based` | 以工作區為基礎的資源目前處於公開預覽狀態，可讓您將 Application Insights 的遙測資料傳送至一般的 Log Analytics 工作區。 如需詳細資訊，請參閱 [有關以工作區為基礎之資源的文章](create-workspace-resource.md)。

> [!NOTE]
> 雖然您可以在不同的資源群組之間使用相同的資源名稱，但使用全域唯一名稱可能很有説明。 如果您打算 [執行跨資源查詢](../log-query/cross-workspace-query.md#identifying-an-application) ，因為它會簡化必要的語法，這會很有用。

在必要欄位中輸入適當的值，然後選取 [ **審核 + 建立**]。

![在 [必要欄位] 中輸入值，然後選取 [審核 + 建立]。](./media/create-new-resource/review-create.png)

當您的應用程式建立完成後，新的窗格隨即開啟。 您可以在此窗格中看到受監視應用程式的效能和使用方式資料。 

## <a name="copy-the-instrumentation-key"></a>複製檢測金鑰

檢測金鑰會識別您想要與遙測資料相關聯的資源。 您將需要複製檢測金鑰，並將它新增至您的應用程式程式碼。

> [!IMPORTANT]
> 新的 Azure 區域 **需要** 使用連接字串，而不是檢測金鑰。 [連接字串](./sdk-connection-string.md?tabs=net) 會識別您想要與遙測資料相關聯的資源。 它也可讓您修改您的資源將用來做為遙測目的地的端點。 您必須複製連接字串，並將它加入應用程式的程式碼或加入環境變數。

## <a name="install-the-sdk-in-your-app"></a>在應用程式中安裝 SDK

在應用程式中安裝 Application Insights SDK 核心。 此步驟高度仰賴於應用程式的類型。

使用檢測金鑰來設定[您在應用程式中安裝的 SDK][start]。

SDK 包含可在不需要撰寫任何額外程式碼的情況下傳送遙測的標準模組。 若要更詳細追蹤使用者動作或診斷問題，請[使用 API][api] 來傳送您自己的遙測。

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

如需此 Cmdlet 的完整 PowerShell 檔，以及若要瞭解如何取得檢測金鑰，請參閱 [Azure PowerShell 檔](/powershell/module/az.applicationinsights/new-azapplicationinsights)。

### <a name="azure-cli-preview"></a>Azure CLI (preview) 

若要存取預覽 Application Insights Azure CLI 命令，您必須先執行：

```azurecli
 az extension add -n application-insights
```

如果您未執行 `az extension add` 命令，將會看到錯誤訊息，指出： `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

現在您可以執行下列程式碼來建立您的 Application Insights 資源：

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

如需此命令的完整 Azure CLI 檔，以及瞭解如何取得檢測金鑰，請參閱 [Azure CLI 檔](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)。

## <a name="next-steps"></a>後續步驟
* [診斷搜尋](./diagnostic-search.md)
* [探索度量](../platform/metrics-charts.md)
* [撰寫分析查詢](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

