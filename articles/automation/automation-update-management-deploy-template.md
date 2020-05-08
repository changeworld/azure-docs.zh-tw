---
title: 使用 Azure Resource Manager 範本來上架更新管理 |Microsoft Docs
description: 您可以使用 Azure Resource Manager 範本將 Azure 自動化更新管理解決方案上架。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: dd8706c1e95e6b1e4ca4a38d4a336f6186464696
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872198"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本將更新管理解決方案上架

您可以使用[Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)，在您的資源群組中啟用 Azure 自動化更新管理解決方案。 本文提供可自動化下列專案的範例範本：

* 建立 Azure 監視器 Log Analytics 工作區。
* 建立 Azure 自動化帳戶。
* 將自動化帳戶連結至 Log Analytics 工作區（如果尚未連結）。
* 將 Azure 自動化更新管理解決方案上架。

此範本不會將一或多個 Azure 或非 Azure Vm 的上架自動化。

如果您已在訂用帳戶的支援區域中部署 Log Analytics 工作區和自動化帳戶，則不會連結。 工作區尚未部署更新管理解決方案。 使用此範本會成功建立連結並部署更新管理解決方案。 

>[!NOTE]
>**Nxautomation**使用者上架做為 Linux 更新管理的一部分，只會執行已簽署的 runbook。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="api-versions"></a>API 版本

下表列出此範本中所使用資源的 API 版本。

| 資源 | 資源類型 | API 版本 |
|:---|:---|:---|
| 工作區 | workspaces | 2017-03-15-preview |
| 自動化帳戶 | automation | 2015-10-31 | 
| 解決方法 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>使用範本之前

如果您選擇在本機安裝和使用 PowerShell，本文需要 Azure PowerShell Az 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)以建立與 Azure 的連線。 使用 Azure PowerShell 時，部署會使用[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 版2.1.0 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI，此部署會使用[az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

JSON 範本已設定為提示您輸入：

* 工作區的名稱
* 要在其中建立工作區的區域
* 自動化帳戶的名稱
* 要在其中建立帳戶的區域

JSON 範本會針對您的環境中可能用於標準設定的其他參數，指定預設值。 您可以將範本儲存在 Azure 儲存體帳戶中，以便在您的組織中共用存取。 如需使用範本的進一步資訊，請參閱[使用 Resource Manager 範本部署資源和 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)。

會使用 Log Analytics 工作區的預設值來設定範本中的下列參數：

* SKU - 預設為在 2018 年 4 月定價模型中發行的全新每 GB 定價層
* 資料保留期-預設為三十天
* 容量保留-預設為 100 GB

>[!WARNING]
>如果在已選擇加入 2018 年 4 月全新定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 **PerGB2018**。
>

JSON 範本會為您環境中可能用來做為標準設定的其他參數，指定預設值。 您可以將範本儲存在 Azure 儲存體帳戶中，以便在您的組織中共用存取。 如需使用範本的進一步資訊，請參閱[使用 Resource Manager 範本部署資源和 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)。

如果您不熟悉 Azure 自動化和 Azure 監視器，請務必瞭解下列設定詳細資料，以便在嘗試建立、設定和使用連結至新自動化帳戶的 Log Analytics 工作區時，避免發生錯誤。

* 請參閱[其他詳細資料](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)，以充分瞭解工作區設定選項，例如存取控制模式、定價層、保留期和容量保留層級。

* 因為只有特定區域支援連結 Log Analytics 工作區和您訂用帳戶中的自動化帳戶，所以請參閱[工作區](how-to/region-mappings.md)對應，以指定內嵌或參數檔案中支援的區域。

* 如果您不熟悉 Azure 監視器記錄檔，而且尚未部署工作區，您應該參閱[工作區設計](../azure-monitor/platform/design-logs-deployment.md)指引以瞭解存取控制，並瞭解我們建議您的組織使用的設計實作為策略。

## <a name="deploy-template"></a>部署範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. 編輯範本以符合您的需求。 請考慮建立[Resource Manager 參數](../azure-resource-manager/templates/parameter-files.md)檔案，而不是以內嵌值傳遞參數。

3. 將此檔案儲存到本機資料夾，做為**deployUMSolutiontemplate**。

4. 您已準備好部署此範本。 您可以使用 PowerShell 或 Azure CLI。 當系統提示您輸入工作區和自動化帳戶名稱時，請提供在所有 Azure 訂用帳戶中都是全域唯一的名稱。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    部署需要幾分鐘的時間才能完成。 完成後，您會看到類似下列包含結果的訊息：

    ![部署完成時的範例結果](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>後續步驟

現在您已部署更新管理解決方案，您可以啟用 Vm 進行管理、檢查更新評估，以及部署更新，使其符合規範。

- 從您的[Azure 自動化帳戶](automation-onboard-solutions-from-automation-account.md)，取得一或多個 azure 機器，並針對非 Azure 機器手動進行。

- 針對來自 Azure 入口網站中 [虛擬機器] 頁面的單一 Azure VM。 此案例適用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm。

- 針對[多個 Azure vm](manage-update-multi.md) ，請從 Azure 入口網站的 [**虛擬機器**] 頁面中選取它們。 