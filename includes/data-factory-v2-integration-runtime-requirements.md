---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544275"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果您的資料存放區位於內部部署網路、Azure 虛擬網路或 Amazon 虛擬私人雲端中，您必須設定[自我裝載整合運行](../articles/data-factory/create-self-hosted-integration-runtime.md)時間來與其連線。

如果您的資料存放區是受管理的雲端資料服務，其中的存取權僅限於防火牆規則中列入允許清單的 Ip，您可以使用 Azure integration runtime，並將[其 ip](../articles/data-factory/azure-integration-runtime-ip-addresses.md)新增至允許清單。 

如需 Data Factory 存取資料存放區所支援之網路安全性機制的詳細資訊，請參閱[資料存取策略](../articles/data-factory/data-access-strategies.md)。
