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
ms.openlocfilehash: 9ed918c841989cb50efa362251d09c0b655b7fc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570040"
---
| 資源 | 限制 |
| ---------------------------------------------------------------------- | -------------------------- |
| 縮放單位數上限 | 每個區域 10 個<sup>1</sup> |
| 快取大小 | 每個單位 5 GiB<sup>2</sup> |
| 每個 HTTP 授權單位的並行後端連線數目<sup>3</sup> | 每個單位 2048 個<sup>4</sup> |
| 最大快取回應大小 | 2 MiB |
| 原則文件大小上限 | 256 KiB<sup>5</sup> |
| 每個服務執行個體的自訂閘道網域數目上限<sup>6</sup> | 20 |
| 每個服務執行個體的 CA 憑證數目上限<sup>7</sup> | 10 |
| 每個訂用帳戶的服務執行個體數目上限<sup>8</sup> | 20 |
| 每個服務執行個體的訂用帳戶數目上限<sup>8</sup> | 500 |
| 每個服務執行個體的用戶端憑證數目上限<sup>8</sup> | 50 |
| 每個服務執行個體的 API 數目上限<sup>8</sup> | 50 |
| 每個服務執行個體的 API 作業數目上限<sup>8</sup> | 1,000 |
| 總要求持續時間上限<sup>8</sup> | 30 秒 |
| 緩衝承載大小上限<sup>8</sup> | 2 MiB |
| 要求 URL 大小上限<sup>9</sup> | 4096 個位元組 |
| 自我裝載閘道數目上限<sup>10</sup> | 25 |

<sup>1</sup>調整限制取決於定價層。 如需定價層及其調整限制的詳細資訊，請參閱 [API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>2</sup>每一單位快取大小取決於定價層。 若要查看定價層及其調整限制，請參閱 [API 管理價格](https://azure.microsoft.com/pricing/details/api-management/)。<br/>
<sup>3</sup>除非後端明確關閉連線，否則連線會經過集區化和重複使用。<br/>
<sup>4</sup>限制是根據基本、標準和進階層的每個單位。 開發人員層的限制為 1024 個。 此限制不適用於使用層。<br/>
<sup>5</sup>此限制適用於基本、標準和進階層。 在使用層原則中，文件大小限制為 4 KiB。<br/>
<sup>6</sup>只有開發人員和進階層支援多個自訂網域。<br/>
<sup>7</sup>使用層不支援 CA 憑證。<br/>
<sup>8</sup>此限制僅適用於使用層。 其他層級的這些類別沒有任何限制。<br/>
<sup>9</sup>僅適用於使用層。 包含最多 2048 個位元組長的查詢字串。<br/>
<sup>10</sup>只有開發人員和進階層支援自我裝載閘道。 此限制適用於[自我裝載閘道資源](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway)數目。 若要提高此限制，請連絡[支援](https://azure.microsoft.com/support/options/)。 請注意，在進階層中，與自我裝載閘道資源相關聯的節點 (或複本) 數目沒有限制，而在開發人員層的單一節點上具有上限。
