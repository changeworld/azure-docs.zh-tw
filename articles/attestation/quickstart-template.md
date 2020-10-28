---
title: 使用 Azure Resource Manager 範本建立 Azure 證明憑證
description: 了解如何使用 Azure Resource Manager 範本建立 Azure 證明憑證。
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144982"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>快速入門：使用 ARM 範本建立 Azure 證明提供者

[Microsoft Azure 證明](overview.md)是用於證明信任執行環境 (TEE) 的解決方案。 本快速入門著重於部署 Azure Resource Manager 範本 (ARM 範本) 來建立 Microsoft Azure 證明原則的程序。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-attestation-provider-create)。

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

範本中定義的 Azure 資源：

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. 選取或輸入下列值。

    除非另有指定，否則使用預設值來建立證明提供者。

    - **證明提供者名稱** ：選取 Azure 證明提供者的名稱。
    - **位置** ：選取位置。 例如， **美國中部** 。
    - **標記** ：選取位置。 例如， **美國中部** 。

1. 選取 [購買]。 成功部署證明資源之後，您會收到通知。

Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

您可以使用 Azure 入口網站來檢查證明資源。

## <a name="clean-up-resources"></a>清除資源

其他 Azure 證明會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。

當不再需要時，請刪除資源群組，這會刪除證明資源。 若要使用 Azure CLI 或 Azure PowerShell 刪除資源群組：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 ARM 範本建立了證明資源，然後驗證了部署。 若要深入了解 Azure 證明，請參閱 [Azure 證明的概觀](overview.md)。
