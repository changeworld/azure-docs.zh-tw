---
title: 範例 API 管理原則-使用應用程式閘道時的 IP 位址篩選
titleSuffix: Azure API Management
description: Azure API 管理原則範例-示範如何在使用應用程式閘道時，篩選要求的 IP 位址。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75942473"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>使用應用程式閘道時篩選要求 IP 位址

本文說明 Azure API 管理原則範例，示範如何在透過應用程式閘道或其他媒介存取 API 管理實例時，篩選要求 IP 位址。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [存取限制原則](../api-management-access-restriction-policies.md)
+ [原則範例](../policy-samples.md)
