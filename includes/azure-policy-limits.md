---
title: 包含檔案
description: 包含檔案
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466809"
---
Azure 原則的每個物件類型都有最大計數。 _範圍_的項目表示訂用帳戶或[管理群組](../articles/governance/management-groups/overview.md)。

| Where | 何事 | 最大計數 |
|---|---|---|
| 影響範圍 | 原則定義 | 500 |
| 影響範圍 | 計畫定義 | 100 |
| 租用戶 | 計畫定義 | 2,500 |
| 影響範圍 | 原則或方案指派 | 100 |
| 原則定義 | 參數 | 20 |
| 計畫定義 | 原則 | 100 |
| 計畫定義 | 參數 | 100 |
| 原則或方案指派 | 排除項目 (notScopes) | 400 |
| 原則規則 | 巢狀的條件 | 512 |
| 補救工作 | 資源 | 500 |
