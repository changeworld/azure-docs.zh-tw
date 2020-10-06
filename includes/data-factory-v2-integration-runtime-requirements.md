---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91672284"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果您的資料存放區位於內部部署網路、Azure 虛擬網路或 Amazon 虛擬私人雲端中，則必須設定[自我裝載整合執行階段](../articles/data-factory/create-self-hosted-integration-runtime.md)以與其連線。

或者，如果您的資料存放區是受控雲端資料服務，則可以使用 Azure Integration Runtime。 如果僅限防火牆規則中核准的 IP 才能進行存取，則可將 [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) 新增至允許清單。 

如需 Data Factory 支援的網路安全性機制和選項的詳細資訊，請參閱[資料存取策略](../articles/data-factory/data-access-strategies.md)。
