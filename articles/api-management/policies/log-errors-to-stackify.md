---
title: 示例 API 管理原則 - 將錯誤發送到堆疊以進行日誌記錄
titleSuffix: Azure API Management
description: Azure API 管理原則範例 - 示範如何新增錯誤記錄原則，以將錯誤傳送到 Stackify 進行記錄。
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442448"
---
# <a name="send-errors-to-stackify-for-logging"></a>將錯誤傳送至 Stackify 進行記錄

本文會說明 Azure API 管理原則範例，示範如何新增錯誤記錄原則，以將錯誤傳送到 Stackify 進行記錄。 若要設定或編輯原則程式碼，請依照[設定或編輯原則](../set-edit-policies.md)中所述的步驟執行。 若要查看其他範例，請參閱[原則範例](../policy-samples.md)。

## <a name="policy"></a>原則

將程式碼貼至 [on-error]**** 區塊。

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>後續步驟

深入了解 APIM 原則：

+ [轉換原則](../api-management-transformation-policies.md)
+ [原則範例](../policy-samples.md)

