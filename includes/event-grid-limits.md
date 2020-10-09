---
title: 包含檔案
description: 包含檔案
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859282"
---
下列限制適用於 Azure 事件方格**主題** (系統、自訂和合作夥伴主題)。 

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的自訂主題 | 100 |
| 每個主題的事件訂用帳戶 | 500 |
| 自訂或合作夥伴主題 (輸入) 的發佈速率 | 5000 個事件/秒或 1 MB/秒 (以先達到者為準) |
| 事件大小 | 1 MB  |
| 每個主題的私人端點連線數  | 64 | 
| 每個主題的 IP 防火牆規則數 | 16 | 

下列限制適用於 Azure 事件方格**網域**。 

| 資源 | 限制 |
| --- | --- |
| 每個事件網域的主題數 | 100,000 |
| 網域內每個主題的事件訂用帳戶數 | 500 |
| 網域範圍事件訂用帳戶 | 50 |
| 事件網域的發佈速率 (輸入) | 5000 個事件/秒或 1 MB/秒 (以先達到者為準) |
| 每個 Azure 訂用帳戶的事件網域數 | 100 |
| 每個網域的私人端點連線數 | 64 | 
| 每個網域的 IP 防火牆規則數 | 16 | 


