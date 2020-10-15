---
title: API 管理原則範例-根據訊息主體大小的路由要求
titleSuffix: Azure API Management
description: Azure API 管理原則範例 - 示範如何根據要求主體的大小進行要求的路由傳送。
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
ms.openlocfilehash: e59aece0be39bf671e3d1e62f1a0e4c2a42a1ec8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076465"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>根據要求主體的大小進行要求的路由傳送

本文會說明 Azure API 管理原則範例，示範如何根據要求主體的大小進行要求的路由傳送。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-reference.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-reference.md)