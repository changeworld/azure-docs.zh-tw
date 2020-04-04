---
title: 包含檔案
description: 包含檔案
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 5608683578a796ae0fffff7544a749043e8698fd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656193"
---
| 資源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 縮放單位數上限 | 每個區域 10 個<sup>1</sup> |
| 快取大小 | 每單位 5 GiB<sup>2</sup> |
| 每個 HTTP 權限的併發後端<sup>連接 3</sup> | 每單位 2,048<sup>4</sup> |
| 最大快取回應大小 | 2 米B |
| 原則文件大小上限 | 256 KiB<sup>5</sup> |
| 每個服務執行個體的自訂閘道網域數目上限<sup>6</sup> | 20 |
| 每個服務實體的最大憑證數 | 10 |
| 每個訂用帳戶的服務執行個體數目上限<sup>7</sup> | 20 |
| 每個服務執行個體的訂用帳戶數目上限<sup>7</sup> | 500 |
| 每個服務執行個體的用戶端憑證數目上限<sup>7</sup> | 50 |
| 每個服務執行個體的 API 數目上限<sup>7</sup> | 50 |
| 每個服務執行個體的 API 作業數目上限<sup>7</sup> | 1,000 |
| 最大總要求持續時間<sup>7</sup> | 30 秒 |
| 緩衝承載大小上限<sup>7</sup> | 2 米B |
| 最大要求網址大小<sup>8</sup> | 4096 位元組 |

<sup>1</sup>縮放限制取決於定價層。 要檢視定價層及其縮放限制,請參閱[API 管理定價](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>單位緩存大小取決於定價層。 要檢視定價層及其縮放限制,請參閱[API 管理定價](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非後端顯式關閉,否則連接將彙集並重用。<br/>
<sup>4</sup>此限制是基本、標準和高級層的單位。 開發人員層限制為 1,024。 此限制不適用於"消耗"層。<br/>
<sup>5</sup>此限制適用於基本、標準和高級層。 在"消費"層中,策略文檔大小限制為 16 KiB。<br/>
<sup>6</sup>此資源僅在高級層中可用。<br/>
<sup>7</sup>此資源僅適用於"消耗"層。<br/>
<sup>8</sup>僅適用於"消耗"層。 包括最多 2048 位元組長的查詢字串。<br/>
