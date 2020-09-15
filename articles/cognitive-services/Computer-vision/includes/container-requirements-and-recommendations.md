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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877964"
---
> [!NOTE]
> 需求和建議是以每秒單一要求的基準測試為基礎，使用已掃描之商務信件的 8 MB 影像（包含29行，共803個字元）。

下表說明每個讀取容器的資源的最小和建議配置。

| 容器 | 最小值 | 建議 |Tps<br> (最小值、最大值) |
|-----------|---------|-------------|--|
| 讀取 | 1核心，8 GB 記憶體，0.24 TPS | 8核心，16 GB 記憶體，1.17 TPS | 0.24、1.17 |

* 每個核心必須至少 2.6 GHz 或更快。
* TP-每秒交易數。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
