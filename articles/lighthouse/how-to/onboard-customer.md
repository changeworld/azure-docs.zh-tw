---
title: 讓客戶在 Azure Lighthouse 上線
description: 瞭解如何將客戶上線至 Azure 燈塔，讓他們的資源可透過您自己的租使用者存取及管理，並使用 Azure 委派的資源管理。
ms.date: 05/26/2020
ms.topic: how-to
ms.openlocfilehash: cac40a835ff3227a31611b31655865d43fa378ab
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118870"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>讓客戶在 Azure Lighthouse 上線

本文說明身為服務提供者，您可以將客戶上線至 Azure 燈塔。 當您這麼做時，客戶的委派資源 (訂用帳戶和/或資源群組) 可以透過您自己的 Azure Active Directory (Azure AD) 租使用者使用[Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)來存取和管理。

如果您在管理多個客戶的資源，您可以重複此程序。 然後，當授權的使用者登入您的租使用者時，該使用者就可以在客戶租用範圍內獲得授權，以執行管理作業，而不需要登入每個個別的客戶租使用者。

為了追蹤您對客戶參與的整體影響及接受表彰，您可將 Microsoft 合作夥伴網路 (MPN) 識別碼與能存取每個已上線訂用帳戶的至少一個使用者帳戶建立關聯。 請注意，您必須在服務提供者租用戶中執行此關聯。 為了簡化作業，建議在租用戶中建立與 MPN 識別碼建立關聯的服務主體帳戶，並授與其每個上架客戶的讀取存取權。 如需詳細資訊，請參閱[將合作夥伴識別碼連結到 Azure 帳戶](../../cost-management-billing/manage/link-partner-id.md)。 

> [!NOTE]
> 當客戶購買受管理的服務供應專案時，也可以上架至 Azure 燈塔， (您發佈至 Azure Marketplace 的公用或私用) 。 如需詳細資訊，請參閱[將受控服務供應項目發佈到 Azure Marketplace](publish-managed-services-offers.md)。 您也可以搭配發佈至 Azure Marketplace 的供應項目來使用這裡所描述上線程序。

上線程序需要您在服務提供者的租用戶與客戶的租用戶內都採取動作。 此文章中描述所有這些步驟。

> [!TIP]
> 雖然我們在本主題中參考服務提供者和客戶，但[管理多個](../concepts/enterprise.md)租使用者的企業可以使用相同的程式來設定 Azure 燈塔，併合並其管理體驗。

## <a name="gather-tenant-and-subscription-details"></a>收集租用戶與訂用帳戶詳細資料

若要讓客戶的租用戶上線，它必須有有效的 Azure 訂用帳戶。 您將需要知道下列項目：

- 服務提供者租用戶的租用戶識別碼 (您將在其中管理客戶的資源)
- 客戶租用戶的租用戶識別碼 (其中包含受服務提供者管理的資源)
- 客戶租用戶中將由服務提供者管理的每個特定訂用帳戶 (或所含資源群組將會由服務提供者管理的訂用帳戶)，其訂用帳戶識別碼。

> [!NOTE]
> 即使只想要在訂用帳戶中將一或多個資源群組上線，部署也必須在訂用帳戶層級完成，因此您將需要訂用帳戶識別碼。

如果還沒有這些識別碼值，您可使用下列其中一種方式來擷取。 請務必在部署中使用這些確切值。

### <a name="azure-portal"></a>Azure 入口網站

將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄] 就能查看您的租用戶識別碼。 若要選取並複製您的租使用者識別碼，請在入口網站中搜尋 "Azure Active Directory"，然後選取 [屬性]，並複製 [目錄識別碼] 欄位中顯示的值。 若要尋找客戶租用戶中的訂用帳戶識別碼，請搜尋「訂用帳戶」，然後選取適當的訂用帳戶識別碼。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> 當您使用此處所述的程序讓訂用帳戶 (或訂用帳戶內的一或多個資源群組) 上線時，**Microsoft.ManagedServices** 資源提供者就會針對該訂用帳戶進行註冊。

## <a name="define-roles-and-permissions"></a>定義角色與權限

身為服務提供者，您可以對單一客戶執行多個工作，每個都要求存取不同範圍。 您可以定義所需數量的授權，以便將[角色型存取控制 (RBAC) 內建角色](../../role-based-access-control/built-in-roles.md)指派給租用戶中的使用者。

若要讓管理工作更容易，我們建議針對每個角色使用 Azure AD 使用者群組，而不是將權限直接指派給該使用者，讓您可將個別使用者新增至群組或從中移除。 您也可以將角色指派給服務主體。 請務必遵循最少特殊權限準則，讓使用者只具備完成其工作所需的權限。 如需有關所支援角色的建議和資訊，請參閱 [Azure Lighthouse 中的租用戶、使用者和角色案例](../concepts/tenants-users-roles.md)。

> [!IMPORTANT]
> 若要新增 Azure AD 群組的許可權，[群組類型] 必須是 [安全性]，而不是 [Office 365]。 建立群組時，會選取此選項。 如需詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

為了定義授權，您必須知道在要授與存取權的服務提供者租用戶中，每個使用者、使用者群組或服務主體的識別碼值。 您也會需要所要指派每個內建角色的角色定義識別碼。 如果還沒有識別碼值，您可從服務提供者租用戶內，執行下列命令來擷取。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> 建議您在讓客戶上線時，指派[受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，讓您租用戶中的使用者之後可以視需要，[移除委派的存取權](remove-delegation.md)。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。

## <a name="create-an-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

若要讓客戶上線，您必須為供應項目建立包含下列資訊的 [Azure Resource Manager](../../azure-resource-manager/index.yml) 範本。 當客戶在 Azure 入口網站的[服務提供者頁面](view-manage-service-providers.md)中檢視供應項目詳細資料時，會顯示 **mspOfferName** 和 **mspOfferDescription** 值。

|欄位  |定義  |
|---------|---------|
|**mspOfferName**     |說明此定義的名稱。 此值會以供應項目標題的形式向客戶顯示。         |
|**mspOfferDescription**     |供應項目的簡短描述 (例如「Contoso VM 管理供應項目」)。      |
|**managedByTenantId**     |您的租用戶識別碼。          |
|**authorizations**     |來自租用戶使用者/群組/SPN 的 **principalId** 值，每個都包含 **principalIdDisplayName**，以協助客戶了解授權的目的，且對應至內建 **roleDefinitionId** 值以指定存取層級。      |

上線程序必須使用提供在[範例存放庫](https://github.com/Azure/Azure-Lighthouse-samples/)中的 Azure Resource Manager 範本，以及經過修改以符合設定並定義授權的對應參數檔案。

您選擇的範本將取決於是要讓整個訂用帳戶、一個資源群組，還是訂用帳戶內的多個資源群組上線。 如果您偏好使用範本讓已購買受控服務供應項目 (您發佈至 Azure Marketplace) 的客戶上線，我們也提供可用於此情況的範本。

|若要讓項目上線  |使用此 Azure Resource Manager 範本  |並修改此參數檔案 |
|---------|---------|---------|
|訂用帳戶   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|資源群組   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|訂用帳戶內的多個資源群組   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|訂用帳戶 (使用發佈至 Azure Marketplace 的供應項目時)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> 這裡所述的程序需要針對每個上線訂用帳戶進行個別訂用帳戶層級部署，即使您是在相同客戶租用戶內，將訂用帳戶上線也一樣。 如果要在相同客戶租用戶內的不同訂用帳戶中讓多個資源群組上線，您也需要個別部署。 不過，在單一訂用帳戶內讓多個資源群組上線可在一次訂用帳戶層級的部署中完成。
>
> 將多個供應項目套用至相同的訂用帳戶 (或訂用帳戶內的資源群組) 時，也需要個別部署。 所套用的每個供應專案必須使用不同的 **mspOfferName**。

下列範例顯示經過修改且可用於訂用帳戶上線的 **delegatedResourceManagement.parameters.json** 檔案。 與資源群組參數檔案 (位於 [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) 資料夾) 類似，但還包含 **rgName** 參數，以識別要上線的特定資源群組。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

上述範例的最後一個授權會新增具有「使用者存取系統管理員」角色 (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) 的 **principalId**。 當您指派此角色時，必須包含 **delegatedRoleDefinitionIds** 屬性與一或多個內建角色。 在此授權中所建立使用者能夠將這些內建角色指派給客戶租用戶中的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，在[部署可補救的原則](deploy-policy-remediation.md)時需要這些角色。  使用者也可以建立支援事件。  通常與「使用者存取系統管理員」角色相關聯的其他權限都不會套用至此使用者。

## <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure Resource Manager 範本

更新參數檔案之後，客戶租用戶中使用者必須將其租用戶中的 Azure Resource Manager 範本部署為訂用帳戶層級部署。 您想要上架 (的每個訂用帳戶，或包含您想要上架) 之資源群組的每個訂用帳戶，都需要個別部署。

這是訂用帳戶層級部署，因此無法在 Azure 入口網站中起始。 您可以使用 PowerShell 或 Azure CLI 完成部署，如下所示。

> [!IMPORTANT]
> 這個訂用帳戶層級部署必須由客戶租用戶中的非來賓帳戶執行，且該租用戶對於要上線的訂用帳戶必須有[「擁有者」內建角色](../../role-based-access-control/built-in-roles.md#owner) (機器翻譯) (或其包含要上線的資源群組)。 若要查看可委派訂用帳戶的所有使用者，客戶租用戶中的使用者可以在 Azure 入口網站中選取訂用帳戶並開啟 [存取控制 (IAM)]，然後[查看所有具有「擁有者」角色的使用者](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。
>
> 如果訂用帳戶是透過[雲端解決方案提供者 (CSP) 計畫](../concepts/cloud-solution-provider.md)所建立，則在服務提供者租用戶中具有[系統管理代理人](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何使用者，都可以執行部署。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>確認上線成功

當客戶訂用帳戶已成功上架至 Azure 燈塔時，如果使用者已透過上述程式（個別或以具有適當許可權) 的 Azure AD 群組成員）授與該訂用帳戶及其資源，則該服務提供者的租使用者中的使用者將能夠查看其 (。 若要確認，請檢查並確定訂用帳戶以下列其中一種方式出現。  

### <a name="azure-portal"></a>Azure 入口網站

在服務提供者的租用戶中：

1. 瀏覽至 [[我的客戶] 頁面](view-manage-customers.md)。
2. 選取 [客戶]。
3. 請確認您可以看到訂用帳戶顯示為您在 Resource Manager 範本中所提供的名稱。

> [!IMPORTANT]
> 若要在[我的客戶](view-manage-customers.md)中查看委派的訂用帳戶，服務提供者的租使用者中的使用者必須已被授與[讀取](../../role-based-access-control/built-in-roles.md#reader)者角色 (或另一個內建角色，包括上架訂用帳戶時) 讀取器存取權。

在客戶租用戶中：

1. 瀏覽至 [[服務提供者] 頁面](view-manage-service-providers.md)。
2. 選取 [服務提供者供應項目]。
3. 請確認您可以看到訂用帳戶顯示為您在 Resource Manager 範本中所提供的名稱。

> [!NOTE]
> 您部署完成之後可能需要幾分鐘，更新才會反映在 Azure 入口網站。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]，以[檢視及管理客戶](view-manage-customers.md)。
- 了解如何[移除先前上線的委派存取權](remove-delegation.md)。
