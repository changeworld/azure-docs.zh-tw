---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629505"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果您的資料存放區是以下列其中一種方式設定，您就必須設定[自我裝載整合運行](../articles/data-factory/create-self-hosted-integration-runtime.md)時間來連接到資料存放區：

- 資料存放區位於內部部署網路內、Azure 虛擬網路內或 Amazon 虛擬私用雲端內。
- 資料存放區是受管理的雲端資料服務，其中的存取權僅限於防火牆規則中列入允許清單的 Ip。
