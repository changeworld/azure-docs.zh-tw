---
title: 適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項
description: 列出適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項。 這些內建原則定義提供管理 Azure 資源合規性的常見方法。
ms.date: 01/25/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 1f06865fc0b4f982c2e2653e471c75d2a1f88c9e
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795430"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項

如果您使用 [Azure 原則](../governance/policy/overview.md)在 [Azure 安全性基準測](../security/benchmarks/introduction.md)中強制執行建議，您可能已經知道可以建立原則來識別及修正不符合規範的服務。 這些原則可能是自訂的，也可能是以內建定義為基礎，為廣泛使用的最佳做法提供合規性準則和適當的解決方案。

針對 Azure 認知搜尋，目前有一個可在原則指派中使用的內建定義 (如下所列)。 內建用於記錄和監視。 在[您建立的原則](../governance/policy/assign-policy-portal.md)中使用此內建定義，系統會掃描沒有[診斷記錄](search-monitor-logs.md)的搜尋服務，然後據以啟用。

[Azure 原則中的法規合規性](../governance/policy/concepts/regulatory-compliance.md)可針對與不同合規性標準相關的 **合規性網域** 和 **安全性控制**，提供 Microsoft 建立和管理的方案定義 (稱為「內建項目」)。 此頁面會列出適用於 Azure 認知搜尋的 **合規性網域** 和 **安全性控制項**。 您可以針對 **安全性控制項** 個別指派內建項目，以協助讓您的 Azure 資源符合特定標準的規範。

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 原則法規合規性](../governance/policy/concepts/regulatory-compliance.md)。
- 請參閱 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的內建項目。
