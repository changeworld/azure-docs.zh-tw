---
title: Azure API 管理原則範例 - 篩選回應內容 | Microsoft Docs
description: Azure API 管理原則範例 - 示範如何根據與要求相關聯的產品，從回應承載篩選資料元素。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 1f2794c2d72dd460f0b3edf5fb7ec4035746c6e4
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078437"
---
# <a name="filter-response-content"></a>篩選回應內容

本文會說明 Azure API 管理原則範例，示範如何根據與要求相關聯的產品，從回應承載篩選資料元素。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-reference.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸出]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-reference.md)