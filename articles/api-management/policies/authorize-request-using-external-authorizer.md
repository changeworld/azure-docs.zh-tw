---
title: API 管理原則範例-使用外部授權者授權要求
titleSuffix: Azure API Management
description: Azure API 管理原則範例 - 示範如何使用外部授權者封裝自訂或傳統驗證/授權邏輯來授權要求。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076227"
---
# <a name="authorize-requests-using-external-authorizer"></a>使用外部授權者授權要求

本文將說明 Azure API 管理原則範例，示範如何使用外部授權者封裝自訂驗證/授權邏輯來保護 API 存取。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-reference.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [存取限制原則](../api-management-access-restriction-policies.md)
+ [原則範例](../policy-reference.md)