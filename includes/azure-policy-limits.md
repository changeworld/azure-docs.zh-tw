---
title: 包含檔案
description: 包含檔案
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: e22594a50a9c0d814ef7d66443f8253f5832cb1d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038495"
---
Azure 原則的每個物件類型都有最大計數。 _範圍_的項目表示訂用帳戶或[管理群組](../articles/governance/management-groups/overview.md)。

| Where | 何事 | 最大計數 |
|---|---|---|
| 影響範圍 | 原則定義 | 500 |
| 影響範圍 | 計畫定義 | 200 |
| 租用戶 | 計畫定義 | 2,500 |
| 影響範圍 | 原則或方案指派 | 200 |
| 原則定義 | 參數 | 20 |
| 計畫定義 | 原則 | 1000 |
| 計畫定義 | 參數 | 100 |
| 原則或方案指派 | 排除項目 (notScopes) | 400 |
| 原則規則 | 巢狀的條件 | 512 |
| 補救工作 | 資源 | 1000 |
