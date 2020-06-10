---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966372"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
若您的資料存放區是以下列其中一種方式設定的，您必須設定[自我裝載整合執行階段](../articles/data-factory/create-self-hosted-integration-runtime.md)以連線到此資料存放區：

- 資料存放區位於內部部署網路內、Azure 虛擬網路內或 Amazon 虛擬私用雲端內。
- 資料存放區是受控雲端資料服務，其中存取權受限於列於防火牆規則白名單中的 IP。
