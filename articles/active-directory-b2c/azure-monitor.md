---
title: 使用 Azure 監視器監視 Azure AD B2C
titleSuffix: Azure AD B2C
description: 瞭解如何使用委派的資源管理來記錄 Azure 監視器的 Azure AD B2C 事件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/05/2020
ms.openlocfilehash: b701449e8cfb7a379522ee6ccb93f5569bd703d8
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045993"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>使用 Azure 監視器監視 Azure AD B2C

使用 Azure 監視器將 Azure Active Directory B2C （Azure AD B2C）登入和[審核](view-audit-logs.md)記錄路由傳送至不同的監視解決方案。 您可以保留記錄以供長期使用，或與協力廠商安全性資訊和事件管理（SIEM）工具整合，以深入瞭解您的環境。

您可以將記錄事件路由至：

* 一個 Azure 儲存體帳戶。
* Azure 事件中樞（並與您的 Splunk 和 Sumo 邏輯實例整合）。
* Azure Log Analytics 工作區（用來分析資料、建立儀表板，以及針對特定事件發出警示）。

![Azure 監視器](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，請使用 Azure PowerShell 模組來部署 Azure Resource Manager 範本。

* [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)版本6.13.1 或更高版本

您也可以使用[Azure Cloud Shell](https://shell.azure.com)，其中包含最新版本的 Azure PowerShell 模組。

## <a name="delegated-resource-management"></a>委派的資源管理

Azure AD B2C 利用[Azure Active Directory 監視](../active-directory/reports-monitoring/overview-monitoring.md)。 若要在 Azure AD B2C 租使用者內的 Azure Active Directory 中啟用*診斷設定*，請使用[委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)。

您在 Azure AD B2C 目錄（**服務提供者**）中授權使用者，以在包含您 Azure 訂用帳戶（**客戶**）的租使用者內設定 Azure 監視器實例。 若要建立授權，請將[Azure Resource Manager](../azure-resource-manager/index.yml)範本部署至包含訂用帳戶的 Azure AD 租使用者。 下列各節將逐步引導您完成此程式。

## <a name="create-a-resource-group"></a>建立資源群組

在包含您的 Azure 訂用帳戶的 Azure Active Directory （Azure AD）租使用者中（*而不*是包含 Azure AD B2C 租使用者的目錄），[建立資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。 輸入下列值：

* **訂用帳戶**：選取您的 Azure 訂用帳戶。
* **資源群組**：輸入資源群組的名稱。 例如， *azure-ad-b2c-監視*。
* **區域**：選取 Azure 位置。 例如，*美國中部*。

## <a name="delegate-resource-management"></a>委派資源管理

接下來，收集下列資訊：

Azure AD B2C 目錄（也稱為租使用者識別碼）的**目錄識別碼**。

1. 以具有*使用者系統管理員*角色（或更高版本）的使用者身分登入[Azure 入口網站](https://portal.azure.com/)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 選取 [ **Azure Active Directory**]，然後選取 [**屬性**]。
1. 記錄**目錄識別碼**。

您想要將*參與者*許可權授與您稍早在包含訂用帳戶的目錄中建立之資源群組的 Azure AD B2C 群組或使用者的**物件識別碼**。

為了簡化管理，建議您針對每個角色使用 Azure AD 使用者*群組*，讓您可以在群組中新增或移除個別使用者，而不是直接將許可權指派給該使用者。 在此逐步解說中，您會新增使用者。

1. 在 Azure 入口網站中仍然選取**Azure Active Directory** ，選取 [**使用者**]，然後選取使用者。
1. 記錄使用者的**物件識別碼**。

### <a name="create-an-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

若要讓您的 Azure AD 租使用者（**客戶**）上線，請使用下列資訊為您的供應專案建立[Azure Resource Manager 範本](../lighthouse/how-to/onboard-customer.md)。 當您在 Azure 入口網站的 [[服務提供者] 頁面](../lighthouse/how-to/view-manage-service-providers.md)中查看供應專案詳細資料時，就會顯示 `mspOfferName` 和 `mspOfferDescription` 值。

| 欄位   | 定義 |
|---------|------------|
| `mspOfferName`                     | 說明此定義的名稱。 例如， *Azure AD B2C 的受控服務*。 此值會以供應項目標題的形式向客戶顯示。 |
| `mspOfferDescription`              | 您的供應專案簡短描述。 例如，會*在 Azure AD B2C 中啟用 Azure 監視器*。|
| `rgName`                           | 您稍早在 Azure AD 租使用者中建立的資源組名。 例如， *azure-ad-b2c-監視*。 |
| `managedByTenantId`                | Azure AD B2C 租使用者的**目錄識別碼**（也稱為租使用者識別碼）。 |
| `authorizations.value.principalId` | B2C 群組或使用者的**物件識別碼**，將可存取此 Azure 訂用帳戶中的資源。 在此逐步解說中，請指定您稍早記錄的使用者物件識別碼。 |

下載 Azure Resource Manager 範本和參數檔案：

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

接下來，使用您稍早記錄的值來更新參數檔案。 下列 JSON 程式碼片段顯示 Azure Resource Manager 範本參數檔案的範例。 針對 `authorizations.value.roleDefinitionId`，請使用*參與者角色*的[內建角色](../role-based-access-control/built-in-roles.md)值，`b24988ac-6180-42a0-ab88-20f7382dd24c`。

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

更新參數檔案之後，請將 Azure Resource Manager 範本部署至 Azure 租使用者，做為訂用帳戶層級部署。 這是訂用帳戶層級部署，因此無法在 Azure 入口網站中起始。 您可以使用 Azure PowerShell 模組或 Azure CLI 來進行部署。 Azure PowerShell 方法如下所示。

使用[[Disconnect-azaccount]](/powershell/azure/authenticate-azureps)登入包含訂用帳戶的目錄。 使用 `-tenant` 旗標來強制驗證正確的目錄。

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

使用[get-azsubscription](/powershell/module/az.accounts/get-azsubscription) Cmdlet，列出目前帳戶可在 Azure AD 租使用者下存取的訂用帳戶。 將您想要投影的訂用帳戶識別碼記錄到您的 Azure AD B2C 租使用者中。

```PowerShell
Get-AzSubscription
```

接下來，切換至您要投影到 Azure AD B2C 租使用者的訂用帳戶：

``` PowerShell
Select-AzSubscription <subscription ID>
```

最後，部署您先前下載並更新的 Azure Resource Manager 範本和參數檔案。 據以取代 `Location`、`TemplateFile`和 `TemplateParameterFile` 值。

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

成功部署範本會產生類似下列的輸出（為了簡潔起見，輸出已截斷）：

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

部署範本之後，可能需要幾分鐘的時間，資源投射才會完成。 您可能需要等候幾分鐘（通常不超過五個），然後再移到下一節來選取訂用帳戶。

## <a name="select-your-subscription"></a>選取您的訂用帳戶

部署範本並等候幾分鐘的時間完成資源投射之後，請將您的訂用帳戶與您的 Azure AD B2C 目錄建立關聯，並執行下列步驟。

1. 如果您目前已登入，**請登出 Azure 入口網站**。 這會執行下列步驟，以在入口網站會話中重新整理您的認證。
1. 使用您的 Azure AD B2C 系統管理帳戶登入[Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示。
1. 選取包含您的訂用帳戶的目錄。

    ![切換目錄](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 確認您已選取正確的目錄和訂用帳戶。 在此範例中，會選取所有目錄和訂用帳戶。

    ![目錄中選取的所有目錄 & 訂用帳戶篩選](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>設定診斷設定

委派資源管理並選取您的訂用帳戶之後，您就可以在 Azure 入口網站中[建立診斷設定](../active-directory/reports-monitoring/overview-monitoring.md)。

若要設定 Azure AD B2C 活動記錄的監視設定：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 選取**Azure Active Directory**
1. 在 [監視] 下方，選取 [診斷設定]。
1. 選取 [ **+ 新增診斷設定**]。

    ![Azure 入口網站中的 [診斷設定] 窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

## <a name="next-steps"></a>後續步驟

如需在 Azure 監視器中新增和設定診斷設定的詳細資訊，請參閱 Azure 監視器檔中的此教學課程：

[教學課程：從 Azure 資源收集和分析資源記錄](/azure-monitor/learn/tutorial-resource-logs.md)
