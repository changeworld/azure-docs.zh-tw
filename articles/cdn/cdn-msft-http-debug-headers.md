---
title: 調試來自微軟的 Azure CDN 的 HTTP 標頭 |微軟文檔
description: 調試緩存請求標頭提供有關應用於請求資產的緩存策略的其他資訊。 這些標頭特定于來自 Microsoft 的 Azure CDN。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814066"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>調試來自微軟的 Azure CDN 的 HTTP 標頭
調試回應標頭`X-Cache`提供了內容從哪層提供的 CDN 堆疊的詳細資訊。 此標頭特定于來自 Microsoft 的 Azure CDN。

### <a name="response-header-format"></a>回應標頭格式

頁首 | 描述
-------|------------
X-Cache： TCP_HIT | 當從 CDN 邊緣緩存提供內容時，將返回此標頭。 
X-Cache： TCP_REMOTE_HIT | 當從 CDN 區域緩存（源遮罩層）提供內容時，將返回此標頭
X-Cache： TCP_MISS | 當緩存未命中時，將返回此標頭，並且內容從"原點"提供。 


