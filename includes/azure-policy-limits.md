---
title: 包含檔案
description: 包含檔案
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91605827"
---
Azure 原則的每個物件類型都有最大計數。 對於定義，_範圍_項目表示[管理群組](../articles/governance/management-groups/overview.md)或訂用帳戶。
針對指派和豁免，_範圍_項目表示[管理群組](../articles/governance/management-groups/overview.md)、訂用帳戶、資源群組或個別資源。

| Where | 何事 | 最大計數 |
|---|---|---|
| 影響範圍 | 原則定義 | 500 |
| 影響範圍 | 計畫定義 | 200 |
| 租用戶 | 計畫定義 | 2,500 |
| 影響範圍 | 原則或方案指派 | 200 |
| 影響範圍 | 豁免 | 1000 |
| 原則定義 | 參數 | 20 |
| 計畫定義 | 原則 | 1000 |
| 計畫定義 | 參數 | 100 |
| 原則或方案指派 | 排除項目 (notScopes) | 400 |
| 原則規則 | 巢狀的條件 | 512 |
| 補救工作 | 資源 | 500 |
