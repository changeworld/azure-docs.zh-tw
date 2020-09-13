---
title: 標記資源的原則
description: 描述您可以指派以確保標記相容性的 Azure 原則。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293725"
---
# <a name="assign-policies-for-tag-compliance"></a>指派符合標記規範的原則

您可以使用 [Azure 原則](../../governance/policy/overview.md) 來強制執行標記規則和慣例。 藉由建立原則，您可以避免將資源部署至您的訂用帳戶，但您的組織沒有預期的標記。 您可以建立原則，在部署期間自動套用所需的標籤，而不是手動套用標記或搜尋不符合規範的資源。 您現在也可以使用新的 [修改](../../governance/policy/concepts/effects.md#modify) 效果和 [補救](../../governance/policy/how-to/remediate-resources.md)工作，將標記套用至現有的資源。 下列區段會顯示標籤的範例原則。

## <a name="policies"></a>原則

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>接下來的步驟

* 若要了解如何標記資源，請參閱 [使用標記來組織 Azure 資源](tag-resources.md)。
* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
