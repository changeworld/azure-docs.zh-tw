---
title: API 管理原則範例-新增包含相互關聯識別碼的標頭
titleSuffix: Azure API Management
description: Azure API 管理原則範例 - 示範如何將包含相互關聯識別碼的標頭新增至輸入要求。
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
ms.openlocfilehash: 79910aa60602a80cbe79c4ce9899f6ff73fbfde9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75422242"
---
# <a name="add-a-header-containing-a-correlation-id"></a>新增包含相互關聯識別碼的標頭

本文會說明 Azure API 管理原則範例，示範如何將包含相互關聯識別碼的標頭新增至輸入要求。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

