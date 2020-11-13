---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578806"
---
[Azure 私人端點](../articles/private-link/private-endpoint-overview.md)是一種網路介面，可讓您私密安全地連線到 Azure Private Link 所提供的服務。  私人端點會使用您虛擬網路中的私人 IP 位址，有效地將服務帶入您的虛擬網路。

您可以針對裝載于 [Premium](../articles/azure-functions/functions-premium-plan.md) 和 [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) 方案中的函式使用私用端點。

建立函式的輸入私人端點連線時，您也需要 DNS 記錄來解析私用位址。  依預設，當您使用 Azure 入口網站建立私人端點時，會為您建立私人 DNS 記錄。

若要深入瞭解，請參閱 [使用私用端點進行 Web Apps](../articles/app-service/networking/private-endpoint.md)。