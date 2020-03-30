---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966372"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果資料存儲以以下方式之一配置，則需要設置[自託管集成運行時](../articles/data-factory/create-self-hosted-integration-runtime.md)才能連接到此資料存儲：

- 資料存儲位於本地網路內部、Azure 虛擬網路內或 Amazon 虛擬私有雲內。
- 資料存儲是一個託管雲資料服務，其中訪問僅限於防火牆規則中白名單的 IP。
