---
title: 標記資源的策略
description: 描述可以分配的 Azure 策略，以確保標記符合性。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147014"
---
# <a name="assign-policies-for-tag-compliance"></a>分配策略以符合標籤

您可以使用[Azure 策略](../../governance/policy/overview.md)強制標記規則和約定。 通過創建策略，可以避免將資源部署到沒有組織預期標記的訂閱中。 您可以創建策略，在部署期間自動應用所需的標記，而不是手動應用標記或搜索不符合的資源。 標記現在也可以應用於具有新的["修改"](../../governance/policy/concepts/effects.md#modify)效果和[修正任務的](../../governance/policy/how-to/remediate-resources.md)現有資源。 下列區段會顯示標籤的範例原則。

## <a name="policies"></a>原則

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>後續步驟

* 若要了解如何標記資源，請參閱 [使用標記來組織 Azure 資源](tag-resources.md)。
* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
