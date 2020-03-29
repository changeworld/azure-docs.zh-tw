---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845989"
---
以下限制適用于 Azure 事件網格系統主題和自訂主題，*而不是*事件域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 發佈請求 | 250/秒 |
| 事件大小 | 1 MB（以多個 64 KB 事件為單位充電） |

以下限制僅適用于事件域。

| 資源 | 限制 |
| --- | --- |
| 每個事件域的主題 | 100,000 |
| 域中每個主題的事件訂閱 | 500 |
| 域範圍事件訂閱 | 50 |
| 事件域（入口）的發佈速率 | 每秒 5，000 個事件 |
| 發佈請求 | 250/秒 |
| 每個 Azure 訂閱的事件域 | 100 |