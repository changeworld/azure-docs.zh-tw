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
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131655"
---
下列限制適用于 Azure 事件方格系統主題和自訂主題，而*不*是事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 發行要求 | 每秒250 |
| 事件大小 | 1 MB。 不過，作業會以 64 KB 的增量計費。 因此，超過 64 KB 的事件會產生作業費用，就好像它們是多個事件一樣。 例如，130 KB 的事件會產生作業，就像是3個不同的事件一樣。  |

下列限制僅適用于事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題 | 100,000 |
| 網域內每個主題的事件訂閱 | 500 |
| 網域範圍事件訂閱 | 50 |
| 事件網域的發佈速率（輸入） | 每秒5000個事件 |
| 發行要求 | 每秒250 |
| 每個 Azure 訂用帳戶的事件網域 | 100 |