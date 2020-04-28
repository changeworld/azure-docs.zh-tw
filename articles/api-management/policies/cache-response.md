---
title: API 管理原則範例-將功能新增至後端服務
titleSuffix: Azure API Management
description: Azure API 管理原則範例：示範如何將功能新增至後端服務。 例如，在天氣預報 API 中接受地點的名稱，而不是緯度和經度。
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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442477"
---
# <a name="add-capabilities-to-a-backend-service"></a>將功能新增至後端服務

本文說明 Azure API 管理原則範例，該範例示範如何將功能新增至後端服務。 例如，在天氣預報 API 中接受地點的名稱，而不是緯度和經度。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [輸入]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

