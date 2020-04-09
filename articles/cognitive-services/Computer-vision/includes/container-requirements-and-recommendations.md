---
title: 容器的需求和建議
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877964"
---
> [!NOTE]
> 要求和建議基於每秒一個請求的基準,使用包含 29 行和總共 803 個字元的掃描業務信函的 8 MB 圖像。

下表描述了每個 Read 容器的最小和建議的資源分配。

| 容器 | 最小值 | 建議 |Tps<br>(最小,最大)|
|-----------|---------|-------------|--|
| 讀取 | 1 個內核,8 GB 記憶體,0.24 TPS | 8 個內核,16-GB 記憶體,1.17 TPS | 0.24, 1.17 |

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒事務。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
