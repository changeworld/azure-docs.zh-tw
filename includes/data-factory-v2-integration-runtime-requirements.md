---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529378"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果您的資料存放區位於內部部署網路、Azure 虛擬網路或 Amazon 虛擬私人雲端中，則必須設定[自我裝載整合執行階段](../articles/data-factory/create-self-hosted-integration-runtime.md)以與其連線。

如果您的資料存放區是受控雲端資料服務，則可以使用 Azure Integration Runtime。 如果僅限防火牆規則中列入允許清單的 IP 才能進行存取，則可以選擇將 [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) 新增至允許清單。 

如需 Data Factory 支援的網路安全性機制和選項的詳細資訊，請參閱[資料存取策略](../articles/data-factory/data-access-strategies.md)。
