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
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982074"
---
> [!NOTE]
> 需求和建議是以每秒單一要求的基準測試為基礎，使用已掃描之商務信件的 8 MB 影像（包含29行，共803個字元）。

下表說明每個讀取容器的資源的最小和建議配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 閱讀 3.0-預覽 | 8核心，16 GB 記憶體 | 8核心，24 GB 記憶體 |
| 閱讀 3.1-預覽 | 8核心，16 GB 記憶體 | 8核心，24 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
