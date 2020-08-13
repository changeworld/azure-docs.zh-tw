---
title: 受管理的虛擬網路 & 受控的私人端點
description: 瞭解 Azure Data Factory 中受管理的虛擬網路和受控私人端點。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: c0f23c864430b6cb2f49f924d5aaa8bde296037c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135958"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory 受控虛擬網路 (preview) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將說明 Azure Data Factory 中的受控虛擬網路和受控私人端點。


## <a name="managed-virtual-network"></a>受控虛擬網路

當您在 Azure Data Factory 受控虛擬網路 (VNET) 中建立 Azure Integration Runtime (IR) 時，整合執行時間將會以受控虛擬網路布建，並會利用私人端點安全地連線到支援的資料存放區。 

在 managed 虛擬網路內建立 Azure IR 可確保資料整合程式已隔離且安全。 

使用受控虛擬網路的優點：

- 透過受控虛擬網路，您可以將管理虛擬網路的負擔卸載至 Azure Data Factory。 您不需要為 Azure Integration Runtime 建立子網，這最終會從您的虛擬網路使用許多私人 Ip，而且需要先進行網路基礎結構規劃。 
- 不需要深入的 Azure 網路知識，即可安全地執行資料整合。 相反地，開始使用安全 ETL 已針對資料工程師簡化。 
- 受控虛擬網路和受控的私用端點可防止資料外泄。 

> [!IMPORTANT]
>目前，受管理的 VNet 僅支援在與 Azure Data Factory 區域相同的區域中。
 

![ADF 受控虛擬網路架構](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>受控私人端點

受控私人端點是在 Azure Data Factory 受控虛擬網路中建立的私用端點，其會建立 Azure 資源的私用連結。 Azure Data Factory 會代表您管理這些私人端點。 

![新增受控私人端點](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory 支援私用連結。 私人連結可讓您存取 Azure (PaaS) 服務 (例如 Azure 儲存體、Azure Cosmos DB、Azure Synapse 分析 (先前 Azure SQL 資料倉儲 # A5。

當您使用私用連結時，您的資料存放區與受控虛擬網路之間的流量，會完全透過 Microsoft 骨幹網路進行。 Private Link 可保護您免受資料外洩風險。 您可以建立私人端點來建立資源的私人連結。

私人端點會使用受控虛擬網路中的私人 IP 位址，以有效地將服務帶入其中。 私人端點會對應至 Azure 中的特定資源，而不是整個服務。 客戶可以限制其組織所核准之特定資源的連線能力。 深入了解[私人連結和私人端點](https://docs.microsoft.com/azure/private-link/)。

> [!NOTE]
> 建議您使用受控私人端點連線到所有資料來源。 
 
> [!WARNING]
> 如果 PaaS 資料存放區 (Blob，ADLS Gen2 SQL DW) 有已針對它建立的私用端點，即使它允許來自所有網路的存取，ADF 也只能使用受控私人端點來存取它。 請務必在這類案例中建立私用端點。 

當您在 Azure Data Factory 中建立受控私用端點時，會以「擱置」狀態建立私用端點連接。 已起始核准工作流程。 私人連結資源的擁有者會負責核准或拒絕連線。

![管理私人端點](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

如果擁有者核准連線，就會建立私人連結。 否則，系統將不會建立私人連結。 無論是哪一種情況，受控私人端點都會以連線狀態來更新。

![核准受控私用端點](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

只有處於已核准狀態的受控私人端點可以將流量傳送到指定的私人連結資源。

## <a name="limitations-and-known-issues"></a>限制與已知問題
### <a name="supported-data-sources"></a>支援的資料來源
以下資料來源支援透過來自 ADF 受控虛擬網路的私用連結進行連接。
- Azure Blob 儲存體
- Azure 表格儲存體
- Azure 檔案
- Azure Data Lake Gen2
- Azure SQL Database (不包含 Azure SQL 受控執行個體) 
- Azure Synapse Analytics (先前稱為 Azure SQL 資料倉儲)
- Azure CosmosDB SQL
- Azure 金鑰保存庫
- Azure Private Link

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>透過來自 ADF 受控虛擬網路的公用端點輸出通訊
- 僅針對輸出通訊開啟埠443。
- Azure 儲存體和 Azure Data Lake Gen2 不支援透過來自 ADF 受控虛擬網路的公用端點連線。

### <a name="other-known-issues"></a>其他已知問題
CosmosDB 連線的 Debug 執行無法運作，包括資料流程 debug 和管線 debug。


## <a name="next-steps"></a>後續步驟

- 教學課程：[使用受控虛擬網路和私人端點建立複製管線](tutorial-copy-data-portal-private.md) 
- 教學課程：[使用受控虛擬網路和私人端點建立對應資料流程管線](tutorial-data-flow-private.md)
