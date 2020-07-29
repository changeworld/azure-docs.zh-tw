---
title: 使用 Azure CLI 建立 Log Analytics 工作區 | Microsoft Docs
description: 了解如何使用 Azure CLI 建立 Log Analytics 工作區，以從您的雲端和內部部署環境啟用管理解決方案和資料收集。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 7421162ed68a879d9f935a3efd5c6267e159a648
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324296"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>使用 Azure CLI 2.0 建立 Log Analytics 工作區

Azure CLI 2.0 用於從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門示範如何使用 Azure CLI 2.0 來部署 Azure 監視器中的 Log Analytics 工作區。 Log Analytics 工作區是 Azure 監視器記錄資料的唯一環境。 每個工作區都有自己的資料存放庫與設定，而且資料來源和解決方案會設定為將其資料儲存在特定的工作區中。 如果您想從下列來源收集資料，就必須要有 Log Analytics 工作區：

* 訂用帳戶中的 Azure 資源  
* System Center Operations Manager 監視的內部部署電腦  
* 來自 Configuration Manager 的裝置集合  
* Azure 儲存體的診斷或記錄資料  

針對其他來源，例如環境中的 Azure VM 和 Windows 或 Linux VM，請參閱下列主題：

* [從 Azure 虛擬機器收集資料](./quick-collect-azurevm.md)
* [從混合式 Linux 電腦收集資料](./quick-collect-linux-computer.md)
* [從混合式 Windows 電腦收集資料](quick-collect-windows-computer.md)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>建立工作區
使用 [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 建立工作區。 下列範例會從您的本機電腦使用 Resource Manager 範本，在 eastus 位置建立工作區。 JSON 範本會設定為只提示您輸入工作區的名稱，並針對您環境中可能作為標準組態使用的其他參數，指定預設值。 或者，您可以將範本儲存在 Azure 儲存體帳戶中，以在組織內共用存取。 如需使用範本的詳細資訊，請參閱 [使用 Resource Manager 範本和 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

如需支援區域的詳細資訊，請參閱[可以使用 Log Analytics 的區域](https://azure.microsoft.com/regions/services/)，並從 [搜尋產品] 欄位搜尋 Azure 監視器。

下列參數會設定預設值：

* 位置 - 預設為美國東部
* SKU - 預設為在 2018 年 4 月定價模型中發行的全新每 GB 定價層

>[!WARNING]
>如果在已選擇加入 2018 年 4 月全新定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 **PerGB2018**。
>

### <a name="create-and-deploy-template"></a>建立和部署範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 編輯範本以符合您的需求。 檢閱 [Microsoft.OperationalInsights/workspaces 範本](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces)參考，以了解支援哪些屬性和值。
3. 將此檔案儲存為本機資料夾的 deploylaworkspacetemplate.json。   
4. 您已準備好部署此範本。 從包含範本的資料夾使用下列命令。 當系統提示您輸入工作區名稱時，請提供在所有 Azure 訂用帳戶全域中都是唯一的名稱。

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

部署需要幾分鐘的時間才能完成。 完成後，您會看到類似下列包含結果的訊息：

![部署完成時的範例結果](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>疑難排解
當您建立在過去 14 天內刪除且處於[虛刪除狀態](../platform/delete-workspace.md#soft-delete-behavior)的工作區時，根據您的工作區設定，此作業可能會有不同的結果：
1. 如果您提供的工作區名稱、資源群組、訂用帳戶和區域與已刪除工作區中的相同，您的工作區將會復原，包括其資料、設定和連接的代理程式。
2. 如果您使用相同的工作區名稱，但是不同的資源群組、訂用帳戶或區域，您將會收到錯誤：「工作區名稱 'workspace-name' 不是唯一的，或是有衝突。」 若要覆寫虛刪除並永久刪除您的工作區，並使用相同的名稱建立新的工作區，請遵循下列步驟先復原工作區，然後執行永久刪除：
   * [復原](../platform/delete-workspace.md#recover-workspace)您的工作區
   * [永久刪除](../platform/delete-workspace.md#permanent-workspace-delete)您的工作區
   * 使用相同工作區名稱建立新的工作區

## <a name="next-steps"></a>後續步驟
有了可用的工作區之後，您可以設定監視遙測的集合、執行記錄搜尋以分析該資料，並且新增管理解決方案，以提供額外的資料和分析深入解析。  

* 若要從具有 Azure 診斷或 Azure 儲存體的 Azure 資源啟用資料收集，請參閱[收集 Azure 服務的記錄和計量以便使用於 Log Analytics](../platform/resource-logs.md#send-to-log-analytics-workspace)。  
* [新增 System Center Operations Manager 作為資料來源](../platform/om-agents.md)，以從會報告 Operations Manager 管理群組的代理程式收集資料，並且將其儲存在 Log Analytics 工作區中。  
* 連線 [Configuration Manager](../platform/collect-sccm.md) 以匯入階層中集合成員的電腦。  
* 檢閱可用的[監視解決方案](../insights/solutions.md)，以及如何從您的工作區新增或移除解決方案。

