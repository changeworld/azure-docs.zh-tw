---
title: 示例 API 管理原則 - 使用應用程式閘道時對 IP 位址進行篩選
titleSuffix: Azure API Management
description: Azure API 管理原則示例 - 演示如何在使用應用程式閘道時根據請求的 IP 位址進行篩選。
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
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942473"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>使用應用程式閘道時，根據請求 IP 位址進行篩選

本文演示了 Azure API 管理原則示例，該示例演示了如何通過應用程式閘道或其他仲介訪問 API 管理實例時如何篩選請求 IP 位址。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [存取限制原則](../api-management-access-restriction-policies.md)
+ [原則範例](../policy-samples.md)
