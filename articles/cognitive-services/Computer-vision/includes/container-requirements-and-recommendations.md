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
ms.openlocfilehash: a441f677687789729b96011f8bf98606418ca659
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677210"
---
> [!NOTE]
> 需求和建議是以每秒單一要求的基準測試為基礎，使用 8 MB 影像的掃描商務信件，其中包含 29 行及總計 803 個字元。

下表描述每個 Read 容器的資源最低和建議配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 閱讀 2.0-預覽 | 1核心，8 GB 記憶體 |  8 核心，16-GB 記憶體 |
| Read 3.1-預覽 | 8 核心，16-GB 記憶體 | 8 核心，24-GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
