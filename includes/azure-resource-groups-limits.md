---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 949118214851c3eceffd8c1d638a4093bdf7f366
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89304070"
---
| 資源 | 限制 |
| --- | --- |
| 每個[資源群組的資源](../articles/azure-resource-manager/management/overview.md#resource-groups) | 資源不會受到資源群組的限制。 相反地，其會受到資源群組中資源類型的限制。 請參閱下一列。 |
| 每個資源群組的資源、每個資源類型 |800 - 某些資源類型可能超過 800 個限制。 請參閱[資源不限於每個資源群組 800 個執行個體的限制](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md)。 |
| 部署歷程記錄中每個資源群組的部署 |800<sup>1</sup> |
| 每個部署的資源 |800 |
| 管理鎖定 (每個唯一的[範圍](../articles/azure-resource-manager/management/overview.md#understand-scope))  |20 |
| 標記數目 (每個資源或資源群組) |50 |
| 標記金鑰長度 |512 |
| 標記值長度 |256 |

<sup>1</sup>當記錄接近上限時，部署會自動從歷程記錄中刪除。 從部署歷程記錄中刪除項目時，不會影響部署的資源。 如需詳細資訊，請參閱 [從部署歷程記錄自動刪除](../articles/azure-resource-manager/templates/deployment-history-deletions.md)。

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

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱[在部署 Azure 資源時使用連結的範本](../articles/azure-resource-manager/templates/linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。
