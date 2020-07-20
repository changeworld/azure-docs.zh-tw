---
title: 標記資源的原則
description: 說明您可以指派以確保標記合規性的 Azure 原則。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147858"
---
# <a name="assign-policies-for-tag-compliance"></a>指派標記合規性原則

您可以使用[Azure 原則](../../governance/policy/overview.md)來強制執行標記規則和慣例。 藉由建立原則，您可以避免將資源部署到您的訂用帳戶，但您的組織沒有預期的標記。 您不需要手動套用標記或搜尋不符合規範的資源，而是建立一個原則，在部署期間自動套用所需的標記。 標籤現在也可以套用至具有新[修改](../../governance/policy/concepts/effects.md#modify)效果和[補救](../../governance/policy/how-to/remediate-resources.md)工作的現有資源。 下列區段會顯示標籤的範例原則。

## <a name="policies"></a>原則

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>後續步驟

* 若要了解如何標記資源，請參閱 [使用標記來組織 Azure 資源](tag-resources.md)。
* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
