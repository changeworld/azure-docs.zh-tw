---
title: Azure 前門中 HTTP 標頭的協定支援 |微軟文檔
description: 本文介紹了前門支援的 HTTP 標頭協定。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471671"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure 前門中 HTTP 標頭的協定支援
本文概述了前門支援與部分調用路徑的協定（請參見圖像）。 以下各節提供有關前門支援的 HTTP 標頭的詳細資訊。

![Azure 前門 HTTP 標頭協定][1]

>[!IMPORTANT]
>前門不認證此處未記錄的任何 HTTP 標頭。

## <a name="client-to-front-door"></a>用戶端至 Front Door
前門接受來自傳入請求的大多數標頭，而無需修改它們。 如果發送，某些保留標頭將從傳入請求中刪除，包括具有 X-FD-* 首碼的標頭。

## <a name="front-door-to-backend"></a>Front Door 至後端

前門包括來自傳入請求的標頭，除非由於限制而被刪除。 前門還添加了以下標頭：

| 頁首  | 範例和描述 |
| ------------- | ------------- |
| Via |  通過： 1.1 Azure </br> 前門添加用戶端的 HTTP 版本，後跟*Azure*作為 Via 標頭的值。 此標頭指示用戶端的 HTTP 版本，並且前門是用戶端和後端之間請求的中間收件者。  |
| X-Azure-用戶端IP | X-Azure-用戶端IP： 127.0.0.1 </br> 表示與正在處理的請求關聯的用戶端 IP 位址。 例如，來自代理的請求可能會添加 X-前轉 -For 標頭以指示原始調用方的 IP 位址。 |
| X-Azure-通訊端IP |  X-Azure-SocketIP： 127.0.0.1 </br> 表示與當前請求源自的 TCP 連接關聯的通訊端 IP 位址。 請求的用戶端 IP 位址可能不等於其通訊端 IP 位址，因為它可能被使用者任意覆蓋。|
| X-Azure-參考 |  X-Azure-參考： 0zxV_XAAAAKMkmmojBv2NT4TY6SQVjC0zv1NURHRTA2Mtkandm3Yzgyy2Qtmzyyys00YTU0Ltk0Yzmtnwzmza3NjQ3Nzzz </br> 標識前門提供的請求的唯一引用字串。 它用於搜索訪問日誌，對故障排除至關重要。|
| X-Azure 請求鏈 |  X-Azure 請求鏈：躍點 =1 </br> 前門用於檢測請求迴圈的標頭，使用者不應依賴它。 |
| X-Forwarded-For | X 轉發- For： 127.0.0.1 </br> X 轉發 -for （XFF） HTTP 標頭欄位通常標識通過 HTTP 代理或負載等化器連接到 Web 服務器的用戶端的原始 IP 位址。 如果存在現有的 XFF 標頭，則前門會將用戶端通訊端 IP 追加到該標頭中，或者使用用戶端通訊端 IP 添加 XFF 標頭。 |
| X-Forwarded-Host | X 轉發主機：contoso.azurefd.net </br> X-前行主機 HTTP 標頭欄位是一種常用方法，用於標識用戶端在主機 HTTP 要求標頭中請求的原始主機。 這是因為前端伺服器處理請求的主機名稱可能不同。 |
| X-Forwarded-Proto | X 轉發-原型： HTTP </br> X-前行-Proto HTTP 標頭欄位通常用於標識 HTTP 要求的原始協定，因為基於配置的前門可能使用 HTTPS 與後端通信。 即使對反向代理的請求是 HTTP，也是如此。 |
| X-FD-健康探針 | X-FD-HealthProbe HTTP 標頭欄位用於從前門識別運行狀況探測。 如果此標頭設置為 1，則請求為運行狀況探測。 當需要從特定的前門嚴格訪問 X-前置主機頭欄位時，可以使用。 |

## <a name="front-door-to-client"></a>Front Door 至用戶端

從後端發送到前門的任何標頭也會傳遞到用戶端。 以下是從前門發送到用戶端的標頭。

| 頁首  | 範例 |
| ------------- | ------------- |
| X-Azure-參考 |  *X-Azure-參考： 0zxV_XAAAAKMkmmojBv2NT4TY6SQVjC0zv1NURHRTA2Mtkandm3Yzgyy2Qtmzyyys00YTU0Ltk0Yzmtnwzmza3NjQ3Nzzz* </br> 這是唯一的參考字串，可識別由 Front Door 提供服務的要求。 這對於故障排除至關重要，因為它用於搜索訪問日誌。|

## <a name="next-steps"></a>後續步驟

- [創建前門](quickstart-create-front-door.md)
- [前門的工作原理](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
