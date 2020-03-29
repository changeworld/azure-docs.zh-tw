---
title: 容器的需求和建議
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481751"
---
> [!NOTE]
> 要求和建議基於每秒一個請求的基準，使用包含 29 行和總共 803 個字元的掃描業務信函的 8 MB 圖像。

下表描述了每個 Read 容器的最小和建議的資源配置。

| 容器 | 最小值 | 建議 |Tps<br>（最小，最大）|
|-----------|---------|-------------|--|
| 讀取 | 1 個內核，8 GB 記憶體，0.24 TPS | 8 個內核，16-GB 記憶體，1.17 TPS | 0.24, 1.17 |

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒事務。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
