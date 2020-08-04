---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384817"
---
| 資源 | 限制 |
| --- | :--- |
| 每個訂用帳戶每個區域的標準 sku 容器群組 | 100<sup>1</sup> |
| 每個訂用帳戶每個區域的專用 sku 容器群組 | 0<sup>1</sup> |
| 每個容器群組的容器數目 | 60 |
| 每個容器群組的磁碟區數目 | 20 |
| 每個訂用帳戶每個區域的標準 sku 核心 (CPU) | 10<sup>1,2</sup> | 
| 每個訂用帳戶每個區域 K80 GPU 的標準 sku 核心 (CPU) | 18<sup>1,2</sup> |
| 每個訂用帳戶每個區域 P100 或 V100 GPU 的標準 sku 核心 (CPU) | 0<sup>1,2</sup> |
| 每個 IP 的連接埠數目 | 5 |
| 容器執行個體記錄大小 - 執行中的執行個體 | 4 MB |
| 容器執行個體記錄大小 - 已停止的執行個體 | 16 KB 或 1,000 行 |
| 每小時的容器建立 |300<sup>1</sup> |
| 每 5 分鐘的容器建立 | 100<sup>1</sup> |
| 每小時的容器刪除 | 300<sup>1</sup> |
| 每 5 分鐘的容器刪除 | 100<sup>1</sup> |


<sup>1</sup>若要要求提高限制，請建立 [Azure 支援要求][azure-support]。 包括 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)和 [Azure 學生版](https://azure.microsoft.com/offers/ms-azr-0170p/)在內的免費訂用帳戶沒有資格增加限制或配額。 如果您有免費訂用帳戶，則可以[升級](../articles/cost-management-billing/manage/upgrade-azure-subscription.md)到隨用隨付訂用帳戶。<br />
<sup>2</sup>[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶的預設限制。 其他類別類型的限制可能有所不同。<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
