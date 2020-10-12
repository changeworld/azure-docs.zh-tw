---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648808"
---
私人網站存取是指讓您的應用程式只能透過私人網路 (例如 Azure 虛擬網路) 存取。

* 設定服務端點時，私人網站存取適用於[進階](../articles/azure-functions/functions-premium-plan.md)、[耗用量](../articles/azure-functions/functions-scale.md#consumption-plan)，以及 [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) 方案。
    * 服務端點可以根據每個應用程式進行設定，[平台功能] > [網路] > [設定存取限制] > [新增規則]。 現在可以選取 [虛擬網路] 做為 [規則類型]。
    * 如需詳細資訊，請參閱[虛擬網路服務端點](../articles/virtual-network/virtual-network-service-endpoints-overview.md)。
    * 請記住，使用服務端點時，即使已設定虛擬網路整合，您的函式仍會擁有網際網路的完整輸出存取權。
* 您也可以在使用內部負載平衡器 (ILB) 設定的 App Service 環境中，使用私人網站存取。 如需詳細資訊，請參閱[在 App Service Environment 中建立及使用內部負載平衡器](../articles/app-service/environment/create-ilb-ase.md)。

若要了解如何設定私人網站存取，請參閱[建立 Azure Functions 私人網站存取](../articles/azure-functions/functions-create-private-site-access.md)。