---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574200"
---
> [!IMPORTANT]
> Cosmos DB 實例會建立在 __您訂__ 用帳戶中由 Microsoft 管理的資源群組中，以及所需的任何資源。 這表示您必須支付此 Cosmos DB 實例的費用。 受控資源群組的命名格式為 `<AML Workspace Resource Group Name><GUID>`。 如果您的 Azure Machine Learning 工作區使用私人端點，也會為 Cosmos DB 實例建立虛擬網路。 此 VNet 可用來保護 Cosmos DB 與 Azure Machine Learning 之間的通訊。
> 
> * 請勿刪除包含此 Cosmos DB 實例 __的資源群組__ ，或在此群組中自動建立的任何資源。 如果您需要刪除資源群組、Cosmos DB 實例等，您必須刪除使用該資源群組的 Azure Machine Learning 工作區。 刪除相關聯的工作區時，會刪除資源群組、Cosmos DB 實例和其他自動建立的資源。
> * 此 Cosmos DB 帳戶的預設 [要求單位](../articles/cosmos-db/request-units.md)設定為 __8000__ 。
> * 您 __無法提供自己的 VNet 以搭配所建立的 Cosmos DB 實例使用__ 。 您也 __無法修改虛擬網路__ 。 例如，您無法變更它所使用的 IP 位址範圍。