---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334754"
---
| 資源 | 限制 |
| --- | --- |
| [每個部署的 Web 或輔助角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 每個部署的[實例輸入終結點](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 每個部署[的輸入終結點](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 每次部署[的內部終結點](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 每個部署的[託管服務憑證](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>具有 Web 或輔助角色的每個 Azure 雲服務可以有兩個部署，一個用於生產，一個用於暫存。 此限制是指不同角色的數量，即配置。 此限制不是指每個角色的實例數，即縮放。

