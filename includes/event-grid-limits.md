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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845989"
---
下列限制適用于 Azure 事件方格系統主題和自訂主題，而*不*是事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 發行要求 | 每秒250 |
| 事件大小 | 1 MB （以多個 64-KB 的事件計費） |

下列限制僅適用于事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題 | 100,000 家 |
| 網域內每個主題的事件訂閱 | 500 |
| 網域範圍事件訂閱 | 50 |
| 事件網域的發佈速率（輸入） | 每秒5000個事件 |
| 發行要求 | 每秒250 |
| 每個 Azure 訂用帳戶的事件網域 | 100 |