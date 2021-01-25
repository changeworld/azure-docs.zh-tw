---
title: 使用 Azure Resource Manager 範本 (ARM 範本) 來建立並啟用 Azure DDoS 保護計劃。
description: 了解如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立並啟用 Azure DDoS 保護計劃。
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 3d6f1707ec354cbcceb8c400cfb55f6e143f4cad
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224535"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>快速入門：使用 ARM 範本建立 Azure DDoS 保護標準

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立分散式阻斷服務 (DDoS) 保護計劃和虛擬網路 (VNet)，然後為 VNet 啟用保護計劃。 Azure DDoS 保護標準計劃會定義一組跨訂用帳戶且已啟用 DDoS 保護的虛擬網路。 您可以為組織設定一個 DDoS 保護計劃，然後將來自多個訂用帳戶的虛擬網路連結至該相同計劃。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans)。

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

此範本會定義兩個資源：

- [Microsoft.Network/ddosProtectionPlans](/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>部署範本

在此範例中，範本會建立新的資源群組、DDoS 保護計劃和 VNet。

1. 選取 [部署至 Azure] 按鈕以登入 Azure 並開啟範本。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. 輸入值以建立新的資源群組、DDoS 保護計劃和 VNet 名稱。

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS 快速入門範本。":::

    - 訂用帳戶：將部署資源的 Azure 訂用帳戶名稱。
    - **資源群組**：選取現有的資源群組或建立新的資源群組。
    - **區域**：部署資源群組的區域，例如「美國東部」。
    - **DDoS 保護計劃名稱**：新 DDoS 保護計劃的名稱。
    - **虛擬網路名稱**：為新的 VNet 建立名稱。
    - **位置**：使用資源群組所在區域來部署資源的函式。
    - **Vnet 位址首碼**：使用預設值，或輸入您的 VNet 位址。
    - **子網路首碼**：使用預設值，或輸入您的 VNet 子網路。
    - **已啟用 DDoS 保護計劃**：預設值為 `true`，表示已啟用 DDoS 保護計劃。

1. 選取 [檢閱 + 建立]。
1. 確認已通過範本驗證，然後選取 [建立] 以開始部署。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

若要複製 Azure CLI 或 Azure PowerShell 命令，請選取 [複製] 按鈕。 按一下 [試試看] 按鈕可開啟 Azure Cloud Shell 來執行命令。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

輸出會顯示新的資源。

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>清除資源

當您完成時，您可以刪除資源。 此命令會刪除資源群組以及其中包含的所有資源。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>後續步驟

若要了解如何檢視及設定 DDoS 保護計劃的遙測，請繼續進行教學課程。

> [!div class="nextstepaction"]
> [檢視和設定 DDoS 保護遙測](telemetry.md)
