---
title: " (預覽版設定私人端點) "
titleSuffix: Azure Machine Learning
description: 使用 Azure Private Link 從虛擬網路安全地存取您的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: 9ce139131e2c6cbfd73f9160b986d9886ae4844b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181947"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>設定 Azure Machine Learning 工作區的 Azure Private Link (預覽) 

在本檔中，您將瞭解如何搭配使用 Azure Private Link 與您的 Azure Machine Learning 工作區。 

> [!IMPORTANT]
> 使用 Azure Private Link 搭配 Azure Machine Learning 工作區目前處於公開預覽狀態。 這項功能僅適用于 **美國東部**、 **美國中南部** 和 **美國西部 2** 區域。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Private Link 可讓您使用私人端點連接到工作區。 私人端點是虛擬網路內的一組私人 IP 位址。 然後，您可以將工作區的存取許可權制為只在私人 IP 位址上進行。 Private Link 有助於降低資料遭到外泄的風險。 若要深入了解私人端點，請參閱 [Azure Private Link](/azure/private-link/private-link-overview) 一文。

> [!IMPORTANT]
> Azure Private Link 不會影響 Azure 控制平面 (管理作業) 例如刪除工作區或管理計算資源。 例如，建立、更新或刪除計算目標。 這些作業會正常地在公用網際網路上執行。
>
> 如果您使用 Mozilla Firefox，您可能會遇到嘗試存取工作區私人端點的問題。 此問題可能與 Mozilla 的 HTTPS DNS 相關。 我們建議使用 Google Chrome Microsoft Edge 作為因應措施。

> [!TIP]
> Azure Machine Learning 計算實例可以與工作區和私人端點搭配使用。 這項功能目前在 **美國東部**、 **美國中南部** 和 **美國西部 2** 區域處於公開預覽狀態。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>建立使用私人端點的工作區

> [!IMPORTANT]
> 目前，我們只支援在建立新的 Azure Machine Learning 工作區時啟用私人端點。

的範本 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 可以用來建立具有私人端點的工作區。

如需使用此範本的詳細資訊（包括私人端點），請參閱 [使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。

## <a name="using-a-workspace-over-a-private-endpoint"></a>使用私人端點上的工作區

因為僅允許從虛擬網路對工作區進行通訊，使用該工作區的任何開發環境都必須是虛擬網路的成員。 例如，虛擬網路中的虛擬機器。

> [!IMPORTANT]
> 為了避免連線暫時中斷，Microsoft 建議在啟用 Private Link 後，在連線到工作區的電腦上清除 DNS 快取。 

如需 Azure 虛擬機器的詳細資訊，請參閱 [虛擬機器檔](/azure/virtual-machines/)。


## <a name="using-azure-storage"></a>使用 Azure 儲存體

若要保護您的工作區所使用的 Azure 儲存體帳戶，請將它放在虛擬網路內。

如需將儲存體帳戶放在虛擬網路中的詳細資訊，請參閱 [使用您工作區的儲存體帳戶](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)。

> [!WARNING]
> Azure Machine Learning 不支援使用已啟用 private link 的 Azure 儲存體帳戶。

## <a name="using-azure-key-vault"></a>使用 Azure Key Vault

若要保護您的工作區所使用的 Azure Key Vault，您可以將它放在虛擬網路內，或為其啟用 Private Link。

如需將金鑰保存庫放入虛擬網路中的詳細資訊，請參閱 [使用金鑰保存庫實例搭配您的工作區](how-to-enable-virtual-network.md#key-vault-instance)。

如需啟用金鑰保存庫 Private Link 的詳細資訊，請參閱 [整合 Key Vault 與 Azure Private Link](/azure/key-vault/private-link-service)。

## <a name="using-azure-kubernetes-services"></a>使用 Azure Kubernetes Services

若要保護您的工作區所使用的 Azure Kubernetes 服務，請將它放在虛擬網路內。 如需詳細資訊，請參閱 [使用 Azure Kubernetes Services 搭配您的工作區](how-to-enable-virtual-network.md#aksvnet)。

Azure Machine Learning 現在支援使用已啟用 private link 的 Azure Kubernetes Service。
若要建立私用 AKS 叢集，請遵循[此處](https://docs.microsoft.com/azure/aks/private-clusters)的檔

## <a name="azure-container-registry"></a>Azure Container Registry

如需保護虛擬網路內 Azure Container Registry 的詳細資訊，請參閱 [使用 Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry)。

> [!IMPORTANT]
> 如果您的 Azure Machine Learning 工作區使用 Private Link，並將工作區的 Azure Container Registry 放在虛擬網路中，您也必須套用下列 Azure Resource Manager 範本。 此範本可讓您的工作區透過 Private Link 與 ACR 進行通訊。

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

如需保護 Azure Machine Learning 工作區安全的詳細資訊，請參閱 [企業安全性](concept-enterprise-security.md) 文章。