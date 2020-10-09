---
title: Azure Kinect 主體索引對應
description: 瞭解如何在 Azure Kinect DK 中查詢內文追蹤索引對應。
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、移植、主體、追蹤、索引、分割、地圖
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276529"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Azure Kinect 主體追蹤索引對應

本文索引對應包含深度相機捕捉中每個主體的實例分割地圖。 每個圖元都會對應到深度或 IR 影像中的對應圖元。 每個圖元的值代表圖元所屬的主體。 它可以是背景 (值 `K4ABT_BODY_INDEX_MAP_BACKGROUND`) 或所偵測到的索引 `k4abt_body_t` 。

![主體索引對應範例](./media/concepts/body-index-map.png)

>[!NOTE]
> 主體索引與主體識別碼不同。您可以藉由呼叫 API： [k4abt_frame_get_body_id ( # B1 ](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)，從指定的主體索引查詢主體識別碼。


## <a name="using-body-index-map"></a>使用主體索引對應

主體索引對應會儲存為 `k4a_image_t` ，而且具有與深度或 IR 映射相同的解析度。 每個圖元都是8位值。 您可以藉 `k4abt_frame_t` 由呼叫來從中查詢 `k4abt_frame_get_body_index_map` 。 開發人員負責呼叫來釋出內文索引對應的記憶體 `k4a_image_release()` 。

## <a name="next-steps"></a>後續步驟

[建立第一個全身追蹤應用程式](build-first-body-app.md)
