---
title: " (預覽設定私人端點) "
titleSuffix: Azure Machine Learning
description: 使用 Azure 私人連結，從虛擬網路安全地存取您的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: bdb7ba30d9fa2d0bd1eff9368d6e30e516b53895
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192732"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>為 Azure Machine Learning 工作區設定 Azure 私人連結 (預覽) 

在本檔中，您將瞭解如何搭配使用 Azure 私用連結與您的 Azure Machine Learning 工作區。 

> [!IMPORTANT]
> 使用具有 Azure Machine Learning 工作區的 Azure 私人連結目前為公開預覽狀態。 這項功能僅適用于 **美國東部** 和 **美國西部 2** 區域。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 私人連結可讓您使用私人端點連接到您的工作區。 私人端點是虛擬網路內的一組私人 IP 位址。 接著，您可以將工作區的存取限制為只在私人 IP 位址上進行。 私用連結有助於降低資料外泄的風險。 若要深入了解私人端點，請參閱 [Azure Private Link](/azure/private-link/private-link-overview) 一文。

> [!IMPORTANT]
> Azure 私用連結不會影響 Azure 控制平面 (管理作業) 例如刪除工作區或管理計算資源。 例如，建立、更新或刪除計算目標。 這些作業會在公用網際網路上正常執行。
>
> 已啟用 Private Link 的工作區不支援 Azure Machine Learning 計算執行個體預覽。
>
> 如果您使用 Mozilla Firefox，您可能會遇到嘗試存取工作區之私用端點的問題。 此問題可能與 Mozilla 中的 DNS over HTTPS 有關。 我們建議使用 Google Chrome 的 Microsoft Edge 做為因應措施。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>建立使用私用端點的工作區

> [!IMPORTANT]
> 目前，我們只支援在建立新的 Azure Machine Learning 工作區時啟用私用端點。

中的範本 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 可以用來建立具有私用端點的工作區。

如需使用此範本（包括私人端點）的詳細資訊，請參閱 [使用 Azure Resource Manager 範本來建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。

## <a name="using-a-workspace-over-a-private-endpoint"></a>在私用端點上使用工作區

由於只允許從虛擬網路對工作區進行通訊，因此使用工作區的任何開發環境都必須是虛擬網路的成員。 例如，虛擬網路中的虛擬機器。

> [!IMPORTANT]
> 為了避免暫時中斷連線，Microsoft 建議在啟用私用連結之後，清除連線至工作區之電腦上的 DNS 快取。 

如需 Azure 虛擬機器的詳細資訊，請參閱 [虛擬機器檔](/azure/virtual-machines/)。


## <a name="using-azure-storage"></a>使用 Azure 儲存體

若要保護您的工作區所使用的 Azure 儲存體帳戶，請將它放在虛擬網路中。

如需將儲存體帳戶放在虛擬網路中的相關資訊，請參閱針對 [您的工作區使用儲存體帳戶](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)。

> [!WARNING]
> Azure Machine Learning 不支援使用已啟用私人連結的 Azure 儲存體帳戶。

## <a name="using-azure-key-vault"></a>使用 Azure Key Vault

若要保護您的工作區所使用的 Azure Key Vault，您可以將它放在虛擬網路內，或為其啟用私用連結。

如需將金鑰保存庫放在虛擬網路中的詳細資訊，請參閱搭配 [使用金鑰保存庫實例與您的工作區](how-to-enable-virtual-network.md#key-vault-instance)。

如需啟用金鑰保存庫私人連結的詳細資訊，請參閱 [整合 Key Vault 與 Azure 私人連結](/azure/key-vault/private-link-service)。

## <a name="using-azure-kubernetes-services"></a>使用 Azure Kubernetes Services

若要保護您的工作區所使用的 Azure Kubernetes services，請將它放在虛擬網路中。 如需詳細資訊，請參閱搭配 [使用 Azure Kubernetes Services 與您的工作區](how-to-enable-virtual-network.md#aksvnet)。

Azure Machine Learning 現在支援使用已啟用私用連結的 Azure Kubernetes Service。
若要建立私用 AKS 叢集，請遵循[這裡](https://docs.microsoft.com/azure/aks/private-clusters)的檔

## <a name="azure-container-registry"></a>Azure Container Registry

如需在虛擬網路內保護 Azure Container Registry 的詳細資訊，請參閱 [使用 Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry)。

> [!IMPORTANT]
> 如果您使用 Azure Machine Learning 工作區的私人連結，並將工作區的 Azure Container Registry 放在虛擬網路中，您也必須套用下列 Azure Resource Manager 範本。 此範本可讓您的工作區透過私用連結與 ACR 進行通訊。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟

如需保護 Azure Machine Learning 工作區的詳細資訊，請參閱 [企業安全性](concept-enterprise-security.md) 一文。