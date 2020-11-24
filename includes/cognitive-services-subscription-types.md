---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559860"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
您可以透過兩個不同的資源存取 Azure 認知服務：多服務資源或單一服務資源。

* 多服務資源：
    * 使用單一金鑰和端點存取多個 Azure 認知服務。
    * 合併您所使用服務的帳單。
* 單一服務資源：
    * 針對每個建立的服務，使用唯一的金鑰和端點來存取單一 Azure 認知服務。 
    * 使用免費層來試用服務。