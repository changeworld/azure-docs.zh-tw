---
title: 使用 Azure 監視器監視 Azure AD B2C
titleSuffix: Azure AD B2C
description: 瞭解如何使用委派的資源管理來記錄 Azure 監視器的 Azure AD B2C 事件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 3106e5a640ed66828558078e6986979ad7195450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386210"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>使用 Azure 監視器監視 Azure AD B2C

使用 Azure 監視器將 Azure Active Directory B2C (Azure AD B2C) 登入和 [審核](view-audit-logs.md) 記錄傳送至不同的監視解決方案。 您可以保留記錄以供長期使用，或與協力廠商安全性資訊和事件管理整合 (SIEM) 工具，以深入瞭解您的環境。

您可以將記錄事件路由至：

* Azure [儲存體帳戶](../storage/blobs/storage-blobs-introduction.md)。
* [Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md) (分析資料、建立儀表板，以及) 特定事件的警示。
* Azure [事件中樞](../event-hubs/event-hubs-about.md) (，並與您的 Splunk 和 Sumo 邏輯實例) 整合。

![Azure 監視器](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，請使用 Azure PowerShell 模組部署 Azure Resource Manager 範本。

* [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps) 版本6.13.1 或更高版本

您也可以使用 [Azure Cloud Shell](https://shell.azure.com)，其中包含最新版本的 Azure PowerShell 模組。

## <a name="delegated-resource-management"></a>委派的資源管理

Azure AD B2C 利用 [Azure Active Directory 監視](../active-directory/reports-monitoring/overview-monitoring.md)。 若要在 Azure AD B2C 租使用者內的 Azure Active Directory 中啟用 *診斷設定* ，請使用 [委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)。

您可以授權 Azure AD B2C 目錄中的使用者或群組 (**服務提供者**) 設定租使用者中的 Azure 監視器實例，其中包含 (**客戶**) 的 Azure 訂用帳戶。 若要建立授權，請將 [Azure Resource Manager](../azure-resource-manager/index.yml) 範本部署到包含訂用帳戶的 Azure AD 租使用者。 下列各節將逐步引導您完成此程式。

## <a name="create-or-choose-resource-group"></a>建立或選擇資源群組

這是包含目的地 Azure 儲存體帳戶、事件中樞或 Log Analytics 工作區的資源群組，用來接收 Azure 監視器的資料。 當您部署 Azure Resource Manager 範本時，請指定資源組名。

[建立資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ，或在包含您的 Azure 訂用帳戶的 Azure Active Directory (Azure AD) 租使用者中（ *而不* 是包含您 Azure AD B2C 租使用者的目錄）中選擇現有資源群組。

此範例會在*美國中部*區域使用名為*azure-ad-b2c-monitor*的資源群組。

## <a name="delegate-resource-management"></a>委派資源管理

接下來，請收集下列資訊：

Azure AD B2C 目錄的**目錄識別碼** (也稱為租使用者識別碼) 。

1. 以具有*使用者系統管理員*角色的使用者身分登入[Azure 入口網站](https://portal.azure.com/) (或更高的) 。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 選取 [ **Azure Active Directory**，選取 [ **屬性**]。
1. 記錄 **目錄識別碼**。

Azure AD B2C 群組或使用者的**物件識別碼**，您想要將*參與者*許可權授與您稍早在包含訂用帳戶的目錄中所建立的資源群組。

為了讓管理更容易，我們建議您針對每個角色使用 Azure AD 使用者 *群組* ，讓您可以新增或移除群組中的個別使用者，而不是直接將許可權指派給該使用者。 在這個逐步解說中，您會新增使用者。

1. 在 Azure 入口網站中仍選取了 **Azure Active Directory** ，請選取 [ **使用者**]，然後選取使用者。
1. 記錄使用者的 **物件識別碼**。

### <a name="create-an-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

若要讓您的 Azure AD 租 **使用者 (客戶**) ，請使用下列資訊為您的供應專案建立 [Azure Resource Manager 範本](../lighthouse/how-to/onboard-customer.md) 。 `mspOfferName` `mspOfferDescription` 當您在 Azure 入口網站的 [[服務提供者] 頁面](../lighthouse/how-to/view-manage-service-providers.md)中查看供應專案詳細資料時，會顯示和值。

| 欄位   | 定義 |
|---------|------------|
| `mspOfferName`                     | 說明此定義的名稱。 例如， *Azure AD B2C 受控服務*。 此值會以供應項目標題的形式向客戶顯示。 |
| `mspOfferDescription`              | 您供應專案的簡短描述。 例如，會 *啟用 Azure AD B2C 中的 Azure 監視器*。|
| `rgName`                           | 您稍早在 Azure AD 租使用者中建立的資源組名。 例如， *azure-ad-b2c-監視器*。 |
| `managedByTenantId`                | 您 Azure AD B2C 租使用者的 **目錄識別碼** (也稱為租使用者識別碼) 。 |
| `authorizations.value.principalId` | B2C 群組或使用者的 **物件識別碼** ，可存取此 Azure 訂用帳戶中的資源。 在這個逐步解說中，請指定您稍早記錄的使用者物件識別碼。 |

下載 Azure Resource Manager 範本和參數檔案：

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

接下來，使用您稍早記錄的值來更新參數檔案。 下列 JSON 程式碼片段顯示 Azure Resource Manager 範本參數檔案的範例。 若為 `authorizations.value.roleDefinitionId` ，請使用「*參與者」角色*的[內建角色](../role-based-access-control/built-in-roles.md)值 `b24988ac-6180-42a0-ab88-20f7382dd24c` 。

```json
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

更新您的參數檔案之後，請將 Azure Resource Manager 範本以訂用帳戶層級部署的形式部署至 Azure 租使用者。 這是訂用帳戶層級部署，因此無法在 Azure 入口網站中起始。 您可以使用 Azure PowerShell 模組或 Azure CLI 來部署。 Azure PowerShell 方法如下所示。

使用連線 [-disconnect-azaccount](/powershell/azure/authenticate-azureps)登入包含您訂用帳戶的目錄。 使用 `-tenant` 旗標來強制驗證正確的目錄。

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

使用 [>select-azsubscription 指令程式](/powershell/module/az.accounts/get-azsubscription) 可列出目前帳戶可在 Azure AD 租使用者下存取的訂用帳戶。 將您想要投影的訂用帳戶識別碼記錄到 Azure AD B2C 租使用者中。

```PowerShell
Get-AzSubscription
```

接下來，切換到您想要投射到 Azure AD B2C 租使用者的訂用帳戶：

``` PowerShell
Select-AzSubscription <subscription ID>
```

最後，部署您稍早下載並更新的 Azure Resource Manager 範本和參數檔案。 適當 `Location` 地取代、 `TemplateFile` 和 `TemplateParameterFile` 值。

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

成功部署範本會產生類似以下的輸出 (輸出，以求簡潔) ：

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

部署範本之後，可能需要幾分鐘的時間才能完成資源投射。 您可能需要等候幾分鐘的時間， (通常不超過五個) ，然後再移至下一個區段以選取訂用帳戶。

## <a name="select-your-subscription"></a>選取您的訂用帳戶

當您部署範本，並等候幾分鐘的時間完成資源投射之後，請將您的訂用帳戶與您的 Azure AD B2C 目錄建立關聯，並執行下列步驟。

1. 如果您目前已登入，請**登出**Azure 入口網站。 在入口網站會話中重新整理您的認證時，會執行下列步驟。
1. 使用您 Azure AD B2C 的系統管理帳戶登入 [Azure 入口網站](https://portal.azure.com) 。
1. 選取入口網站工具列中的**目錄 + 訂閱**圖示。
1. 選取包含您訂用帳戶的目錄。

    ![切換目錄](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 確認您已選取正確的目錄和訂用帳戶。 在此範例中，會選取所有目錄和訂閱。

    ![目錄 & 訂用帳戶篩選中選取的所有目錄](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>設定診斷設定

診斷設定會定義應該如何傳送資源的記錄和計量。 可能的目的地包括：

- [Azure 儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)
- [事件中樞](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 解決方案。
- [Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)

如果您還沒有這麼做，請在 [Azure Resource Manager 範本](#create-an-azure-resource-manager-template)中指定的資源群組中，建立所選目的地類型的實例。

### <a name="create-diagnostic-settings"></a>建立診斷設定

您已經準備好在 Azure 入口網站中 [建立診斷設定](../active-directory/reports-monitoring/overview-monitoring.md) 。

若要設定 Azure AD B2C 活動記錄的監視設定：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 選取 **Azure Active Directory**
1. 在 [監視]**** 下方，選取 [診斷設定]****。
1. 如果資源上有現有的設定，您會看到已設定的設定清單。 選取 [ **新增診斷設定** ] 以新增設定，或按一下 [ **編輯** ] 設定來編輯現有的設定。 每個設定都不能有一個以上的目的地類型。

    ![Azure 入口網站中的 [診斷設定] 窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 如果您的設定還沒有名稱，請為您的設定命名。
1. 核取每個目的地的核取方塊，以傳送記錄。 選取 [ **設定** ] 以指定其設定，如下表所述。

    | 設定 | 描述 |
    |:---|:---|
    | 封存至儲存體帳戶 | 儲存體帳戶的名稱。 |
    | 串流至事件中樞 | 建立事件中樞的命名空間 (如果這是您第一次串流記錄) 或串流至 (如果已經有資源將該記錄類別串流至此命名空間) 。
    | 傳送至 Log Analytics | 工作區的名稱。 |

1. 選取 [ **AuditLogs** ] 和 [ **SignInLogs**]。
1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

如需在 Azure 監視器中新增和設定診斷設定的詳細資訊，請參閱 [教學課程：收集和分析 Azure 資源的資源記錄](../azure-monitor/insights/monitor-azure-resource.md)。

如需將 Azure AD 記錄串流至事件中樞的相關資訊，請參閱 [教學課程：將 Azure Active Directory 記錄串流至 Azure 事件中樞](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
