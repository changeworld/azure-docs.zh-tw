---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334754"
---
| 資源 | 限制 |
| --- | --- |
| [每個部署的 Web 或背景工作角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 每個部署的[實例輸入端點](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 每個部署的[輸入端點](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 每個部署的[內部端點](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 每個部署的[託管服務憑證](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>具有 web 或背景工作角色的每個 Azure 雲端服務可以有兩個部署，一個用於生產，另一個用於預備環境。 這項限制是指不同角色的數目，也就是設定。 這項限制不會參考每個角色的實例數目，也就是調整規模。

