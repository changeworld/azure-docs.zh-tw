---
title: 包含檔案
description: 包含檔案
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204661"
---
| 資源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 縮放單位數上限 | 每個區域 10 個<sup>1</sup> |
| 快取大小 | 每個單位 5 GiB<sup>2</sup> |
| 每個 HTTP 授權單位的並行後端連線數<sup>3</sup> | 每單位 2048<sup>4</sup> |
| 最大快取回應大小 | 2 MiB |
| 原則文件大小上限 | 256 KiB<sup>5</sup> |
| 每個服務執行個體的自訂閘道網域數目上限<sup>6</sup> | 20 |
| 每個服務實例的 CA 憑證數目上限<sup>7</sup> | 10 |
| 每個訂用帳戶的服務實例數目上限<sup>8</sup> | 20 |
| 每個服務實例的訂用帳戶數目上限<sup>8</sup> | 500 |
| 每個服務實例的用戶端憑證數目上限<sup>8</sup> | 50 |
| 每個服務實例的 Api 數目上限<sup>8</sup> | 50 |
| 每個服務實例的 API 作業數目上限<sup>8</sup> | 1,000 |
| 總要求持續時間上限<sup>8</sup> | 30 秒 |
| 緩衝承載大小上限<sup>8</sup> | 2 MiB |
| 要求 URL 大小上限<sup>9</sup> | 4096個位元組 |
| 自我裝載閘道的最大數目<sup>10</sup> | 25 |

<sup>1</sup>調整限制取決於定價層。 如需定價層及其調整限制的詳細資訊，請參閱[API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每個單位的快取大小取決於定價層。 若要查看定價層和其調整限制，請參閱[API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非後端明確關閉，否則連線會進行共用並重複使用。<br/>
<sup>4</sup>這項限制是基本、標準和高階層的每個單位。 開發人員層的限制為1024。 此限制不適用於耗用量層。<br/>
<sup>5</sup>此限制適用于基本、標準和 Premium 層。 在取用量層中，原則檔案大小限制為 4 KiB。<br/>
<sup>6</sup>開發人員和 Premium 層僅支援多個自訂網域。<br/>
<sup>7</sup>消費層不支援 CA 憑證。<br/>
<sup>8</sup>此資源僅適用于耗用量層。<br/>
<sup>9</sup>僅適用于耗用量層。 包含最多2048個位元組長的查詢字串。<br/>
<sup>10</sup>只有開發人員和 Premium 層支援自我裝載閘道。 此限制適用于[自我裝載閘道資源](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway)的數目。 若要提高此限制，請聯絡[支援](https://azure.microsoft.com/support/options/)人員。 請注意，在進階層中，與自我裝載閘道資源相關聯的節點（或複本）數目不受限制，而且在開發人員層的單一節點上具有上限。
