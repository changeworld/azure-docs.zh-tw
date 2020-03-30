---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334739"
---
| 資源 | 限制 |
| --- | :--- |
| 每個[訂閱](../articles/billing-buy-sign-up-azure-subscription.md)每個區域的標準 SKU 容器組 | 100<sup>1</sup> |
| 每個[訂閱](../articles/billing-buy-sign-up-azure-subscription.md)每個區域的專用 SKU 容器組 | 0<sup>1</sup> |
| 每個容器群組的容器數目 | 60 |
| 每個容器群組的磁碟區數目 | 20 |
| 每個 IP 的連接埠數目 | 5 |
| 容器執行個體記錄大小 - 執行中的執行個體 | 4 MB |
| 容器執行個體記錄大小 - 已停止的執行個體 | 16 KB 或 1,000 行 |
| 每小時的容器建立 |300<sup>1</sup> |
| 每 5 分鐘的容器建立 | 100<sup>1</sup> |
| 每小時的容器刪除 | 300<sup>1</sup> |
| 每 5 分鐘的容器刪除 | 100<sup>1</sup> |


<sup>1</sup>若要要求提高限制，請建立 [Azure 支援要求][azure-support]。<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
