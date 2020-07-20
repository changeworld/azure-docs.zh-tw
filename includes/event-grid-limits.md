---
title: 包含檔案
description: 包含檔案
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721018"
---
下列限制適用於 Azure 事件方格系統主題和自訂主題，「不」適用於事件網域。

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂主題 (輸入) 的發佈速率 | 每個主題每秒 5,000 個事件 |
| 事件大小 | 1 MB。 不過，作業會以 64 KB 的增量計費。 因此，超過 64 KB 的事件會產生作業費用，就好像是多個事件一樣。 例如，130 KB 的事件會產生作業，就像是 3 個不同的事件一樣。  |
| 每個事件網域的主題數 | 100,000 |
| 網域內每個主題的事件訂用帳戶數 | 500 |
| 網域範圍事件訂用帳戶 | 50 |
| 事件網域的發佈速率 (輸入) | 每秒 5,000 個事件 |
| 每個 Azure 訂用帳戶的事件網域數 | 100 |
| 每個主題或網域的私人端點連線數 | 64 | 
| 每個主題或網域的 IP 防火牆規則數 | 16 | 