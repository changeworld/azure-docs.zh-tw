---
title: 選擇跨區域移動 Azure 資源的工具
description: 檢閱跨區域移動 Azure 資源所用的選項和工具
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603351"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>選擇用來移動 Azure 資源的工具

您可以在 Azure 中移動資源，如下所示：

- **跨區域移動 Azure 資源**：從 Resource Mover 中樞內或資源群組內移動資源。 
- **跨資源群組/訂用帳戶移動資源**：從資源群組內移動。 
- **在 Azure 雲端之間移動資源**：使用 Azure Site Recovery 服務在公用和政府雲端之間移動資源。
- **在相同區域中的可用性區域之間移動資源**：使用 Azure Site Recovery 服務，在相同 Azure 區域中的可用性區域之間移動資源。


## <a name="compare-move-tools"></a>比較移動工具

**工具** | **使用時機** | **深入了解** 
--- | --- | ---
**在資源群組內移動** | 將資源移至不同的資源群組/訂用帳戶或跨區域移動。<br/><br/> 如果您在不同的區域之間移動，請在資源群組中選取您想要移動的資源，並移至 Resource Mover 中樞來驗證相依性，然後將資源移至目的地區域。 | [將資源移至另一個資源群組/訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)。<br/><br/> [將資源從資源群組移至另一個區域](move-region-within-resource-group.md)。
**從 Resource Mover 中樞移動** | 跨區域移動資源。 <br/><br/> 您可以移至目標區域或目標區域內的特定可用性區域或可用性設定組。 | [在 Resource Mover 中樞內跨區域移動資源]()。
**使用 Site Recovery 移動 VM** | 用於在政府和公用雲端之間移動 Azure VM。<br/><br/> 如果您想要在相同區域中的可用性區域之間移動 VM，請使用此方式。 |[在政府/公用雲端之間移動資源](../site-recovery/region-move-cross-geos.md)、[將資源移至相同區域中的可用性區域](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

## <a name="next-steps"></a>後續步驟

[深入了解](about-move-process.md) Resource Mover 元件和移動程序。
