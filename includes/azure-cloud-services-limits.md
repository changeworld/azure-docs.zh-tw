---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838748"
---
| 資源 | 限制 |
| --- | --- |
| [每一部署的 Web 或背景工作角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 每一部署的[執行個體輸入端點](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 每一部署的[輸入端點](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 每一部署的[內部端點](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 每個部署的[託管服務憑證](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>具有 Web 或背景工作角色的每個 Azure 雲端服務均可有兩個部署，一個供生產環境使用，另一個供接移環境使用。 此限制是指不同角色的數目，也就是組態。 此限制不會參考每個角色的執行個體數目，也就是縮放。

