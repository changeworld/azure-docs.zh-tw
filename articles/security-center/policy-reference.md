---
title: 適用於 Azure 資訊安全中心的內建原則定義
description: 列出適用於 Azure 資訊安全中心的 Azure 原則內建原則定義。 這些內建原則定義提供管理 Azure 資源的常見方法。
ms.date: 10/20/2020
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: b7478199e4bb6f1e9ad986193370d4ea8610bd7a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340898"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>適用於 Azure 資訊安全中心的 Azure 原則內建定義

此頁面是與 Azure 資訊安全中心相關的 [Azure 原則](../governance/policy/overview.md) 內建原則定義的索引。 可用的原則定義群組如下：

- [方案](#azure-security-center-initiatives)群組會列出「安全性中心」類別中的 Azure 原則計畫定義。
- [預設的方案](#azure-security-center-initiatives)群組會列出屬於[Azure 資訊安全中心](./index.yml)預設方案一部分的所有 Azure 原則定義。
- [類別目錄](#azure-security-center-category)群組會列出「安全性中心」類別中的所有 Azure 原則定義。

如需有關安全性原則的詳細資訊，請參閱[搭配使用安全性原則](./tutorial-security-policy.md)。 如需其他服務的其他內建 Azure 原則，請參閱 [Azure 原則內建定義](../governance/policy/samples/built-in-policies.md)。

連結至 Azure 入口網站中原則定義的每個內建原則定義名稱。 使用 [版本] 資料行中的連結來查看 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的來源。

## <a name="azure-security-center-initiatives"></a>Azure 資訊安全中心計畫

若要瞭解資訊安全中心所監視的內建計畫，請參閱下表：

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Azure 資訊安全中心預設方案

若要瞭解資訊安全中心所監視的內建原則，請參閱下表：

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure 資訊安全中心分類

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解資訊安全中心 Azure 原則安全性原則定義。 若要深入瞭解，請參閱下列文章。

- 請參閱 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的內建項目。
- 檢閱 [Azure 原則定義結構](../governance/policy/concepts/definition-structure.md)。
- 檢閱[了解原則效果](../governance/policy/concepts/effects.md)。
- [Azure 資訊安全中心規劃與操作指南](./security-center-planning-and-operations-guide.md)：瞭解如何規劃及瞭解 Azure 資訊安全中心中的設計考慮。
- [Azure 資訊安全中心的安全性健全狀況監視](./security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
- [管理與回應 Azure 資訊安全中心的安全性警示](./security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](./security-center-partner-integration.md)：了解如何監視合作夥伴解決方案的健全狀況。
- [Azure 原則](../governance/policy/overview.md)：瞭解如何審核及管理您的 Azure 資源。