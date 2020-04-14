---
title: 調試來自微軟的 Azure CDN 的 HTTP 標頭 |微軟文件
description: 調試快取請求標頭提供有關應用於請求資產的緩存策略的其他資訊。 這些標頭特定於來自 Microsoft 的 Azure CDN。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260406"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>除錯來自微軟的 Azure CDN 的 HTTP 標頭
調試回應標頭`X-Cache`提供了內容從哪層提供的 CDN 堆疊的詳細資訊。 此標頭特定於來自 Microsoft 的 Azure CDN。

### <a name="response-header-format"></a>回應標頭格式

頁首 | 描述
-------|------------
X-Cache: TCP_HIT | 當從 CDN 邊緣緩存提供內容時,將返回此標頭。 
X-Cache: TCP_REMOTE_HIT | 當從 CDN 區域快取(源檢測層)提供內容時,將傳回此標頭
X-Cache: TCP_MISS | 當緩存未命中時,將返回此標頭,並且內容從"原點"提供。 


