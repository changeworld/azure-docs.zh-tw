---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334946"
---
| 資源 | 限制 |
| --- | --- |
| 每個[資源組的資源](../articles/azure-resource-manager/management/overview.md#resource-groups) | 資源不受資源組的限制。 相反，它們受資源組中的資源類型的限制。 請參閱下一行。 |
| 每個資源組的資源，每個資源類型 |800 - 某些資源類型可能超過 800 限制。 請參閱[資源不限於每個資源組 800 個實例](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)。 |
| 部署歷程記錄中每個資源群組的部署 |800<sup>1</sup> |
| 每個部署的資源 |800 |
| 每個唯一範圍的管理鎖 |20 |
| 每個資源或資源組的標記數 |50 |
| 標記金鑰長度 |512 |
| 標記值長度 |256 |

<sup>1</sup>如果達到每個資源組 800 個部署的限制，請從不再需要的歷史記錄中刪除部署。 從部署歷史記錄中項目剪除不會影響已部署的資源。 有關詳細資訊，請參閱[在部署計數超過 800 時解決錯誤](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)。

#### <a name="template-limits"></a>範本限制

| 值 | 限制 |
| --- | --- |
| 參數 |256 |
| 變數 |256 |
| 資源 (包括複本計數) |800 |
| 輸出 |64 |
| 範本運算式 |24,576 個字元 |
| 已匯出範本中的資源 |200 |
| 範本大小 |4 MB |
| 參數檔案大小 |64 KB |

使用巢狀範本，即可超出一些範本限制。 有關詳細資訊，請參閱[在部署 Azure 資源時使用連結範本](../articles/azure-resource-manager/templates/linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
