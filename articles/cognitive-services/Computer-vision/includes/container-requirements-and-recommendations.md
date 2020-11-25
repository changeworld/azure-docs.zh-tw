---
title: 容器的需求和建議
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006897"
---
> [!NOTE]
> 需求和建議是以每秒單一要求的基準測試為基礎，使用 8 MB 影像的掃描商務信件，其中包含 29 行及總計 803 個字元。

下表描述每個 Read 容器的資源最低和建議配置。

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 閱讀 2.0-預覽 | 1核心，8 GB 記憶體 |  8 核心，16-GB 記憶體 |
| 閱讀 3.2-預覽 | 8 核心，16-GB 記憶體 | 8 核心，24-GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
