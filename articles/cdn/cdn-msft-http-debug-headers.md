---
title: 針對來自 Microsoft 的 Azure CDN 進行 HTTP 標頭的偵錯工具 |Microsoft Docs
description: Debug cache 要求標頭會提供已套用至所要求資產之快取原則的其他資訊。 這些標頭是 Microsoft 的 Azure CDN 專屬的標頭。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81260406"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>來自 Microsoft 的 Azure CDN 的 Debug HTTP 標頭
「偵錯工具回應」標頭會 `X-Cache` 提供詳細資料，說明內容提供的 CDN 堆疊層。 此標頭是 Microsoft 的 Azure CDN 專屬的標頭。

### <a name="response-header-format"></a>回應標頭格式

標頭 | 描述
-------|------------
X-快取： TCP_HIT | 從 CDN edge 快取提供內容時，會傳回此標頭。 
X-快取： TCP_REMOTE_HIT | 從 CDN 區域快取 (來源防護層提供內容時，會傳回此標頭) 
X-快取： TCP_MISS | 當發生快取遺漏時，就會傳回此標頭，而且會從來源提供內容。 


