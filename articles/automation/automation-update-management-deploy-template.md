---
title: 使用 Azure Resource Manager 範本啟用更新管理 | Microsoft Docs
description: 此文章說明如何使用 Azure Resource Manager 範本來啟用更新管理。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 0a83117d6d58f45d6ee1de2b8d61c2157738fc75
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830986"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用更新管理

您可以使用 [Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)，來啟用資源群組中的 Azure 自動化更新管理功能。 此文章提供可將下列動作自動化的範例範本：

* 建立 Azure 監視器 Log Analytics 工作區。
* 建立 Azure 自動化帳戶。
* 將自動化帳戶連結至 Log Analytics 工作區 (如果尚未連結)。
* 啟用更新管理。

此範本不會自動啟用一或多個 Azure 或非 Azure VM。

如果您已在訂用帳戶的支援區域中部署 Log Analytics 工作區和自動化帳戶，則不會與其連結。 工作區尚未啟用更新管理。 使用此範本可成功建立連結，並為您的 VM 部署更新管理。 

>[!NOTE]
>在 Linux 上作為更新管理一部分啟用的 **nxautomation** 使用者，只會執行已簽署的 Runbook。

## <a name="api-versions"></a>API 版本

下表列出此範本中使用的資源 API 版本。

| 資源 | 資源類型 | API 版本 |
|:---|:---|:---|
| 工作區 | workspaces | 2017-03-15-preview |
| 自動化帳戶 | automation | 2015-10-31 | 
| 解決方法 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>使用範本之前

如果您選擇在本機安裝並使用 PowerShell，此文章要求使用 Azure PowerShell Az 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需執行 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) \(英文\) 以建立與 Azure 的連線。 使用 Azure PowerShell，部署會使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果選擇本機安裝並使用 CLI，此文章要求您執行 Azure CLI 2.1.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI，此部署會使用 [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) \(英文\)。 

JSON 範本已設定為提示您輸入：

* 工作區的名稱
* 要在其中建立工作區的區域
* 自動化帳戶的名稱
* 要在其中建立帳戶的區域

JSON 範本會針對您的環境中可能用於標準設定的其他參數，指定預設值。 您可以將範本儲存在 Azure 儲存體帳戶中，以在組織內共用存取。 如需使用範本的詳細資訊，請參閱[使用 Resource Manager 範本和 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)。

系統會使用 Log Analytics 工作區的預設值來設定範本中的下列參數：

* SKU - 預設為在 2018 年 4 月定價模型中發行的全新每 GB 定價層
* 資料保留 - 預設為三十天
* 容量保留 - 預設為 100 GB

>[!WARNING]
>如果在已選擇加入 2018 年 4 月全新定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 **PerGB2018**。
>

JSON 範本會針對您的環境中可能用於標準設定的其他參數，指定預設值。 您可以將範本儲存在 Azure 儲存體帳戶中，以在組織內共用存取。 如需使用範本的詳細資訊，請參閱[使用 Resource Manager 範本和 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)。

如果您不熟悉 Azure 自動化和 Azure 監視器，請務必了解下列設定詳細資料，以便在嘗試建立、設定和使用連結至新自動化帳戶的 Log Analytics 工作區時，避免發生錯誤。

* 請檢閱[其他詳細資料](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)，以全面了解工作區設定選項，例如，存取控制模式、定價層、保留和容量保留層級。

* 因為只有特定區域支援連結 Log Analytics 工作區和訂用帳戶中的自動化帳戶，所以，請檢閱[工作區對應](how-to/region-mappings.md)，以內嵌或在參數檔案中指定支援的區域。

* 如果您不熟悉 Azure 監視器記錄，而且尚未部署工作區，您應該檢閱[工作區設計](../azure-monitor/platform/design-logs-deployment.md)指導方真來了解存取控制，並了解我們建議貴組織使用的設計實作策略。

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

2. 編輯範本以符合您的需求。 請考慮建立 [Resource Manager 參數檔案](../azure-resource-manager/templates/parameter-files.md)，而不是以內嵌值傳遞參數。

3. 將此檔案儲存到本機資料夾，例如 **deployUMSolutiontemplate.json**。

4. 您已準備好部署此範本。 您可以使用 PowerShell 或 Azure CLI。 當系統提示您輸入工作區和自動化帳戶名稱時，請提供在所有 Azure 訂用帳戶全域中都是唯一的名稱。

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

* 若要使用 VM 的更新管理，請參閱[管理 Azure VM 的更新和修補程式](automation-tutorial-update-management.md)。
* 如果您不再需要 Log Analytics 工作區，請參閱[從更新管理的自動化帳戶取消連結工作區](automation-unlink-workspace-update-management.md)中的指示。
* 若要從更新管理中刪除 VM，請參閱[從更新管理中移除 VM](automation-remove-vms-from-update-management.md)。
* 若要針對一般更新管理錯誤進行疑難排解，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 若要針對 Windows 更新代理程式的問題進行疑難排解，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 若要針對 Linux 更新代理程式的問題進行疑難排解，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。