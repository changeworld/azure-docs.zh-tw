---
title: 使用 Azure Pipelines 和 Azure 應用程式 Insights 持續監視您的 DevOps 發行管線 |Microsoft Docs
description: 提供使用 Application Insights 快速設定持續監視的指示
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: fd7cd6a107ed45adb60167a57661b60be5dc8212
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86517122"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>將連續監視新增至您的發行管線

Azure Pipelines 整合 Azure 應用程式的見解，以允許在整個軟體發展生命週期中持續監視您的 DevOps 發行管線。 

使用持續監視，發行管線可以納入來自 Application Insights 與其他 Azure 資源的監視資料。 當發行管線偵測到 Application Insights 警示時，管線可以閘道或復原部署，直到警示解決為止。 如果所有檢查都通過，部署可以從測試到生產環境自動繼續，而不需要手動介入。 

## <a name="configure-continuous-monitoring"></a>設定連續監視

1. 在 [Azure DevOps](https://dev.azure.com)中，選取組織和專案。
   
1. 在 [專案] 頁面的左側功能表中，選取 [**管線**  >  **版本**]。 
   
1. 下拉 [ **新增** ] 旁邊的箭號，然後選取 [ **新增發行管線**]。 或者，如果您還沒有管線，請在出現的頁面上選取 [ **新增管線** ]。
   
1. 在 [**選取範本**] 窗格中，搜尋**並選取 [****使用持續監視的 Azure App Service 部署**]，然後選取 [套用]。 

   ![新的 Azure Pipelines 發行管線](media/continuous-monitoring/001.png)

1. 在 [**階段 1** ] 方塊中，選取用來**查看階段**工作的超連結。

   ![檢視階段工作](media/continuous-monitoring/002.png)

1. 在 [ **階段 1** 設定] 窗格中，完成下欄欄位： 

    | 參數        | 值 |
   | ------------- |:-----|
   | **階段名稱**      | 提供階段名稱，或將其保留在 **階段 1**。 |
   | **Azure 訂用帳戶** | 下拉並選取您要使用的連結 Azure 訂用帳戶。|
   | **應用程式類型** | 下拉式清單，然後選取您的應用程式類型。 |
   | **App Service 名稱** | 輸入您的 Azure App Service 名稱。 |
   | **Application Insights 的資源組名**    | 下拉並選取您想要使用的資源群組。 |
   | **Application Insights 資源名稱** | 下拉並選取您所選資源群組的 Application Insights 資源。

1. 若要使用預設的警示規則設定來儲存管線，請選取 Azure DevOps 視窗右上方的 [ **儲存** ]。 輸入描述性批註，然後選取 **[確定]**。

## <a name="modify-alert-rules"></a>修改警示規則

現成的， **使用持續監視範本的 Azure App Service 部署** 有四個警示規則： **可用性**、 **失敗要求**、 **伺服器回應時間**和 **伺服器例外**狀況。 您可以新增更多規則，或變更規則設定，以符合您的服務層級需求。 

若要修改警示規則設定：

在 [發行管線] 頁面的左窗格中，選取 [ **設定 Application Insights 警示**]。

系統會透過內嵌腳本建立四個預設警示規則：

```bash
$subscription = az account show --query "id";$subscription.Trim("`"");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

您可以修改腳本，並新增其他警示規則、修改警示條件，或移除對您的部署而言沒有意義的警示規則。

## <a name="add-deployment-conditions"></a>新增部署條件

當您將部署閘道新增到您的發行管線時，警示會超過您設定的閾值，以防止不必要的發行升級。 一旦您解決警示之後，就可以自動進行部署。

新增部署閘道：

1. 在 [主要管線] 頁面的 [ **階段**] 底下，選取 [ **部署前的條件** ] 或 [ **部署後的條件** ] 符號，取決於哪個階段需要持續監視閘道。
   
   ![部署前的條件](media/continuous-monitoring/004.png)
   
1. 在 [ **部署前的條件** 設定] 窗格中，將 [網 **關** ] 設定為 [ **啟用**]。
   
1. 選取 [ **部署閘道**] 旁的 [ **新增**]。
   
1. 從下拉式功能表中選取 [ **查詢 Azure 監視器警示** ]。 此選項可讓您存取 Azure 監視器和 Application Insights 警示。
   
   ![查詢 Azure 監視器警示](media/continuous-monitoring/005.png)
   
1. 在 [ **評估選項**] 下，輸入您想要的設定值，例如網 **關重新評估之間的時間** ，以及網 **關失敗之後的超時**時間。 

## <a name="view-release-logs"></a>查看發行記錄

您可以在發行記錄檔中看到部署閘道行為和其他發行步驟。 若要開啟記錄：

1. 從 [管線] 頁面的左側功能表中選取 [ **發行** ]。 
   
1. 選取任何版本。 
   
1. 在 [ **階段**] 底下，選取任何階段以查看發行摘要。 
   
1. 若要查看記錄，請選取 [發行摘要] 中的 [ **view logs** ]、在任何階段中選取 [**成功**] 或 [**失敗**] 超連結，或將滑鼠停留在任何階段並選取**記錄** 
   
   ![查看發行記錄](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>接下來的步驟

如需 Azure Pipelines 的詳細資訊，請參閱 [Azure Pipelines 檔](/azure/devops/pipelines)。
