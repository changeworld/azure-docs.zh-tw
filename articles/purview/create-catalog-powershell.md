---
title: 快速入門：使用 Azure PowerShell/Azure CLI 建立 Azure Purview 帳戶 (預覽)
description: 本快速入門說明如何使用 Azure PowerShell/Azure CLI 建立 Azure Purview 帳戶。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 0698295688a4587a704e8cdba0a4796e8d1e6fcd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879994"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>快速入門：使用 Azure PowerShell/Azure CLI 建立 Azure Purview 帳戶

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未發行的版本) 的其他法律條款。

在本快速入門中，您會使用 Azure PowerShell/Azure CLI 建立 Azure Purview 帳戶。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您用來登入 Azure 的使用者帳戶必須為參與者或擁有者角色，或是 Azure 訂用帳戶的管理員。

* 您擁有 [Azure Active Directory 租用戶](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 在用戶端電腦中安裝 Azure PowerShell 或 Azure CLI 來部署範本：[命令列部署](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="configure-your-subscription"></a>設定您的訂用帳戶

如有必要，請遵循下列步驟來設定您的訂用帳戶，讓 Azure Purview 在您的訂用帳戶中執行：

   1. 在 Azure 入口網站中，搜尋並選取 [訂用帳戶]。

   1. 從訂用帳戶的清單中，選取您要使用的訂用帳戶。 需要訂用帳戶的系統管理存取權限。

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="顯示如何在 Azure 入口網站中選取訂用帳戶的螢幕擷取畫面。":::

   1. 對於您的訂用帳戶，選取 **資源提供者**。 在 **資源提供者** 窗格中，搜尋並註冊下列三個資源提供者： 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      如果這些資源提供者未註冊，請選取 [註冊] 以註冊。

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="顯示如何在 Azure 入口網站中註冊 Microsoft.Purview 資源提供者的螢幕擷取畫面。":::

## <a name="create-an-azure-purview-account-instance"></a>建立 Azure Purview 帳戶執行個體

1. 利用您的 Azure 認證登入

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. 如果您有多個 Azure 訂用帳戶，請選取您要使用的訂用帳戶：

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. 為您的 Purview 帳戶建立資源群組。 如果您已經擁有資源群組，則可略過此步驟：

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. 建立 Purview 範本檔案，例如 `purviewtemplate.json`。 您可以更新 `name`、`location` 和 `capacity` (`4` 或 `16`)：

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. 部署 Purview 範本

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    若要執行此部署命令，您必須擁有[最新版本](/cli/azure/install-azure-cli)的 Azure CLI。
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. 此部署命令會傳回結果。 尋找 `ProvisioningState`，以查看部署是否成功。
    
## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Purview 帳戶。

前往下一篇文章，了解如何允許使用者存取您的 Azure Purview 帳戶。 

> [!div class="nextstepaction"]
> [將使用者新增至您的 Azure Purview 帳戶](catalog-permissions.md)