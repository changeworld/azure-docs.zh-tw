---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445186"
---
* 建立新的工作區時，您可以自動建立工作區所需的服務，或使用現有的服務。 如果您想要使用不同于工作區 __之 Azure 訂__ 用帳戶的現有服務，您必須在包含這些服務的訂用帳戶中註冊 Azure Machine Learning 命名空間。 例如，在使用訂用帳戶 B 之儲存體帳戶的訂用帳戶 A 中建立工作區時，必須先在訂用帳戶 B 中註冊 Azure Machine Learning 命名空間，才能將儲存體帳戶用於工作區。

    Azure Machine Learning 的資源提供者為 __MachineLearningService__。 如需如何查看是否已註冊，以及如何註冊的相關資訊，請參閱 [Azure 資源提供者和類型](../articles/azure-resource-manager/management/resource-providers-and-types.md)  文章。

    > [!IMPORTANT]
    > 這僅適用于建立工作區期間所提供的資源;Azure 儲存體帳戶、Azure Container Register、Azure Key Vault 和 Application Insights。