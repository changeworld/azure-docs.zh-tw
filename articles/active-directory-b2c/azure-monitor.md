---
title: 使用 Azure 監視器監視 Azure AD B2C
titleSuffix: Azure AD B2C
description: 瞭解如何使用委派的資源管理使用 Azure 監視器記錄 Azure AD B2C 事件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392882"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>使用 Azure 監視器監視 Azure AD B2C

使用 Azure 監視器將 Azure 活動目錄 B2C (Azure AD B2C) 登入日誌和[審核](view-audit-logs.md)日誌路由到不同的監視解決方案。 您可以保留日誌以長期使用,或與第三方安全資訊和事件管理 (SIEM) 工具集成,從而深入瞭解您的環境。

您可以將紀錄事件路由到:

* Azure[儲存帳戶](../storage/blobs/storage-blobs-introduction.md)。
* Azure[事件中心](../event-hubs/event-hubs-about.md)(並與 Splunk 和相撲邏輯實例集成)。
* [日誌分析工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)(用於分析數據、創建儀錶板和警報特定事件)。

![Azure 監視器](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Prerequisites

要完成本文中的步驟,請使用 Azure PowerShell 模組部署 Azure 資源管理器範本。

* [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)版本 6.13.1 或更高版本

您還可以使用[Azure 雲外殼](https://shell.azure.com),其中包括最新版本的 Azure PowerShell 模組。

## <a name="delegated-resource-management"></a>委派資源管理

Azure AD B2C 利用[Azure 的目錄監視](../active-directory/reports-monitoring/overview-monitoring.md)器 。 在 Azure AD B2C 租戶中的 Azure 活動目錄中啟用*診斷設定*,請使用[委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)。

您可以授權 Azure AD B2C 目錄(**服務提供者**)中的使用者或組組設定包含 Azure 訂閱(**客戶**)的租戶中的 Azure 監視器實例。 要建立授權,請將 Azure[資源管理器](../azure-resource-manager/index.yml)範本部署到包含訂閱的 Azure AD 租戶。 以下各節將引導您完成此過程。

## <a name="create-or-choose-resource-group"></a>建立或選擇資源群組

這是包含目標 Azure 儲存帳戶、事件中心或日誌分析工作區的資源組,用於從 Azure 監視器接收數據。 在部署 Azure 資源管理器樣本時指定資源組名稱。

在 Azure 活動目錄 (Azure AD) 租戶中[創建資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)或選擇包含 Azure 訂閱的現有資源群組,*而不是*包含 Azure AD B2C 租戶的目錄。

此範例*使用位於美國中部*區域的資源組,名為*azure ad-b2c 監視器*。

## <a name="delegate-resource-management"></a>委託資源管理

接下來,收集以下資訊:

Azure AD B2C 目錄(也稱為租戶 ID)的**目錄 ID。**

1. 使用*使用者管理員*角色(或更高版本)以使用者身份登入[Azure 門戶](https://portal.azure.com/)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示,然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇**Azure 的項目目錄**,選擇**屬性**。
1. 記錄**目錄代碼**。

要向前面在包含訂閱的目錄中創建的資源組授予*參與者*許可權的 Azure AD B2C 組或使用者**的物件 ID。**

為了簡化管理,我們建議為每個角色使用 Azure AD 使用者*組*,允許您向該組添加或刪除單個使用者,而不是直接向該使用者分配許可權。 在本演練中,您將添加一個使用者。

1. 在 Azure 門戶中仍選中**Azure 活動目錄**時,請選擇 **「使用者**」,然後選擇使用者。
1. 紀錄使用者**的物件代碼**。

### <a name="create-an-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

要將 Azure AD 租戶(**客戶**)上載,請使用以下資訊為產品/服務建立[Azure 資源管理員樣本](../lighthouse/how-to/onboard-customer.md)。 當您`mspOfferName``mspOfferDescription`在 Azure 門戶的[「服務提供者」頁](../lighthouse/how-to/view-manage-service-providers.md)中查看產品/服務詳細資訊時,和值可見。

| 欄位   | 定義 |
|---------|------------|
| `mspOfferName`                     | 說明此定義的名稱。 例如 *,Azure AD B2C 託管服務*。 此值會以供應項目標題的形式向客戶顯示。 |
| `mspOfferDescription`              | 您的報價的簡要說明。 例如,*在 Azure AD B2C 中啟用 Azure 監視器*。|
| `rgName`                           | 在 Azure AD 租戶中較早之前創建的資源組的名稱。 例如 *,azure ad-b2c 監視器*。 |
| `managedByTenantId`                | Azure AD B2C 租戶(也稱為租戶 ID)的**目錄 ID。** |
| `authorizations.value.principalId` | B2C 組或使用者**的物件 ID,** 這些使用者將有權訪問此 Azure 訂閱中的資源。 在本演練中,指定前面記錄的使用者物件 ID。 |

下載 Azure 資源管理員樣本與參數檔:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

接下來,使用前面記錄的值更新參數檔。 以下 JSON 代碼段顯示了 Azure 資源管理器範本參數檔的範例。 對於`authorizations.value.roleDefinitionId`,使用 *「參與者」角色的*`b24988ac-6180-42a0-ab88-20f7382dd24c`[內建角色](../role-based-access-control/built-in-roles.md)值 。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure Resource Manager 範本

更新參數檔後,將 Azure 資源管理器範本作為訂閱級部署部署到 Azure 租戶中。 這是訂用帳戶層級部署，因此無法在 Azure 入口網站中起始。 可以使用 Azure PowerShell 模組或 Azure CLI 進行部署。 Azure PowerShell 方法如下所示。

使用[Connect-AzAccount](/powershell/azure/authenticate-azureps)登錄到包含訂閱的目錄。 使用標誌`-tenant`強制對正確的目錄進行身份驗證。

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

使用[獲取-Az訂閱](/powershell/module/az.accounts/get-azsubscription)cmdlet 列出當前帳戶在 Azure AD 租戶下可以存取的訂閱。 記錄要投影到 Azure AD B2C 租戶中的訂閱的 ID。

```PowerShell
Get-AzSubscription
```

接下來,切換到要投影到 Azure AD B2C 租戶的訂閱:

``` PowerShell
Select-AzSubscription <subscription ID>
```

最後,部署之前下載和更新的 Azure 資源管理器範本和參數檔。 相應地替換`Location``TemplateFile`和`TemplateParameterFile`值。

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

成功部署樣本可生成類似於以下內容的輸出(輸出為簡潔而截斷):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

部署範本後,可能需要幾分鐘才能完成資源投影。 您可能需要等待幾分鐘(通常不超過五個),然後進入下一節才能選擇訂閱。

## <a name="select-your-subscription"></a>選取您的訂用帳戶

部署範本並等待幾分鐘後資源投影完成後,將訂閱與 Azure AD B2C 目錄相關聯,並執行以下步驟。

1. 如果您目前已登錄,請**註銷**Azure 門戶。 執行此步驟和以下步驟,以在門戶會話中刷新憑據。
1. 使用 Azure AD B2C 管理帳戶登入[Azure 門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇 **「目錄 + 訂閱**」圖示。
1. 選擇包含訂閱的目錄。

    ![切換目錄](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 驗證您選擇了正確的目錄和訂閱。 在此示例中,將選擇所有目錄和訂閱。

    ![在目錄&訂閱篩選器中選擇的所有目錄](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>設定診斷設定

診斷設置定義應發送資源的日誌和指標的位置。 可能的目的地是:

- [Azure 儲存帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)
- [事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)解決方案。
- [Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)

如果尚未,請在[Azure 資源管理器樣本](#create-an-azure-resource-manager-template)中指定的資源組中創建所選目標類型的實例。

### <a name="create-diagnostic-settings"></a>建立診斷設定

您已準備好在 Azure 門戶中[建立診斷設定](../active-directory/reports-monitoring/overview-monitoring.md)。

要設定 Azure AD B2C 活動紀錄的監視設定,請執行以下操作:

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示,然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇**Azure 的項目目錄**
1. 在 [監視]**** 下方，選取 [診斷設定]****。
1. 若資源上有現有設定,您將看到已設定的設定清單。 選擇 **「新增診斷設定**以新增新設定」,或**選擇「編輯**」設定以編輯現有設定。 每個設置只能具有每個目標類型的一個。

    ![Azure 門戶中的診斷設定窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 如果設置尚未具有名稱,請為設置指定名稱。
1. 選中每個目標發送日誌的複選框。 選擇 **「設定**」以按下表中所述指定其設置。

    | 設定 | 描述 |
    |:---|:---|
    | 封存至儲存體帳戶 | 存儲帳戶的名稱。 |
    | 串流至事件中樞 | 創建事件中心(如果這是您第一次流式傳輸日誌)或流式傳輸到的命名空間(如果有資源正在流式傳輸到此命名空間)。
    | 傳送至 Log Analytics | 工作區的名稱。 |

1. 選擇**稽核紀錄**與**登入紀錄**。
1. 選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

有關在 Azure 監視器中新增與設定診斷設定的詳細資訊,請參閱[教學:從 Azure 資源收集和分析資源紀錄](../azure-monitor/insights/monitor-azure-resource.md)。

有關將 Azure AD 紀錄串流式傳輸到事件中心的資訊,請參閱[教學:將 Azure 活動目錄紀錄串流式傳輸到 Azure 事件中心](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
