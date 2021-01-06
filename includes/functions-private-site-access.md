---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936852"
---
[Azure 私人端點](../articles/private-link/private-endpoint-overview.md)是一種網路介面，可讓您私密安全地連線到 Azure Private Link 所提供的服務。  私人端點會使用您虛擬網路中的私人 IP 位址，有效地將服務帶入您的虛擬網路。

您可以針對裝載于 [Premium](../articles/azure-functions/functions-premium-plan.md) 和 [App Service](../articles/azure-functions/dedicated-plan.md) 方案中的函式使用私用端點。

建立函式的輸入私人端點連線時，您也需要 DNS 記錄來解析私用位址。  依預設，當您使用 Azure 入口網站建立私人端點時，會為您建立私人 DNS 記錄。

若要深入瞭解，請參閱 [使用私用端點進行 Web Apps](../articles/app-service/networking/private-endpoint.md)。