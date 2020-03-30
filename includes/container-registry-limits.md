---
title: 包含檔案
description: 包含檔案
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117120"
---
| 資源 | 基本 | 標準 | Premium |
|---|---|---|---|
| 存儲<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 最大圖像圖層大小 | 200 GiB | 200 GiB | 200 GiB |
| 每分鐘的 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10,000 |
| 每分鐘的 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 下載頻寬 Mbps<sup>2</sup> | 30 | 60 | 100 |
| 上傳頻寬 Mbps<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| 異地複寫 | N/A | N/A | [支援][geo-replication] |
| 內容信任 | N/A | N/A | [支援][content-trust] |
| 虛擬網路訪問 | N/A | N/A | [預覽][vnet] |
| 專用鏈路集成 | N/A | N/A | [預覽][plink] |
| 客戶管理的金鑰 | N/A | N/A | [預覽][cmk] |
| 存儲庫範圍許可權 | N/A | N/A | [預覽][token]|
| &bull;令 牌 | N/A | N/A | 20,000 |
| &bull;範圍映射 | N/A | N/A | 20,000 |
| &bull;每個範圍映射的存儲庫 | N/A | N/A | 500 |


<sup>1</sup>指定的存儲限制是每個層*的包含*存儲量。 對於超過這些限制的影像儲存，您需要依每 GiB 的每日費率另外支付費用。 有關費率資訊，請參閱[Azure 容器註冊表定價][pricing]。

<sup>2</sup>*ReadOps*、*WriteOps* 和「頻寬」** 是最小預估值。 Azure 容器註冊表力求根據使用要求提高性能。

<sup>3</sup>[Docker 拉](https://docs.docker.com/registry/spec/api/#pulling-an-image)將根據映射中的圖層數以及清單檢索轉換為多個讀取操作。

<sup>4</sup>[Docker 推送](https://docs.docker.com/registry/spec/api/#pushing-an-image)根據必須推送的層數轉換為多個寫入操作。 `docker push` 包含 *ReadOps*，以擷取現有映像的資訊清單。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md