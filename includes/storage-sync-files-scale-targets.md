---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795913"
---
| 資源 | 目標 | 固定限制 |
|----------|--------------|------------|
| 每個區域的儲存體同步服務數目 | 20 存儲同步服務 | 是 |
| 每個儲存體同步服務的同步群組 | 100 個同步群組 | 是 |
| 每個儲存體同步服務的已註冊伺服器 | 99 部伺服器 | 是 |
| 每個同步組的雲終結點 | 1 個雲端端點 | 是 |
| 每個同步組的伺服器終結點 | 50 個伺服器端點 | 否 |
| 每部伺服器的伺服器端點 | 30 個伺服器端點 | 是 |
| 每個同步群組的檔案系統物件 (目錄和檔案) | 1 億個物件 | 否 |
| 目錄中的檔案系統物件 (目錄和檔案) 數目上限 | 500 萬個物件 | 是 |
| 物件 (目錄和檔案) 安全性描述元大小上限 | 64 KiB | 是 |
| 檔案大小 | 100 GiB | 否 |
| 要分層之檔案的檔案大小下限 | V9：基於檔案系統群集大小（雙檔案系統群集大小）。 例如，如果檔案系統群集大小為 4kb，則最小檔案大小將為 8kb。<br> V8 及更舊： 64 KiB  | 是 |

> [!Note]  
> Azure 檔同步終結點可以擴展到 Azure 檔共用的大小。 如果達到 Azure 檔共用大小限制，則同步將無法操作。
