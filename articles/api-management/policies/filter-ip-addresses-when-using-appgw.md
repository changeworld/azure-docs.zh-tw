---
title: API 管理原則範例-使用應用程式閘道時的 IP 位址篩選
titleSuffix: Azure API Management
description: Azure API 管理原則範例-示範如何在使用應用程式閘道時篩選要求 IP 位址。
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076108"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>使用應用程式閘道時篩選要求 IP 位址

本文說明 Azure API 管理原則範例，示範如何在透過應用程式閘道或其他媒介存取 API 管理實例時，篩選要求 IP 位址。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-reference.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [存取限制原則](../api-management-access-restriction-policies.md)
+ [原則範例](../policy-reference.md)