---
title: Azure Data Factory 的 Azure Private Link
description: 瞭解 Azure Private Link 在 Azure Data Factory 中的運作方式。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596017"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory 的 Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Private Link 可讓您透過私人端點連線到 Azure 中的各種 PaaS 服務。 如需支援 Private Link 功能的 PaaS 服務清單，請移至 [Private Link 檔頁面](https://docs.microsoft.com/azure/private-link/)。 私人端點是特定虛擬網路和子網內的私人 IP 位址。

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>在客戶網路與 Azure Data Factory 服務之間進行安全通訊
若要保護您的 Azure 資源免于公用網路中的攻擊，或讓它們彼此安全地通訊，您可以在雲端中將 Azure 虛擬網路設定為網路的邏輯標記法。 您也可以透過設定 IPSec VPN (站對站) 或 ExpressRoute (私用對等互連) ，將內部部署網路連線到您的虛擬網路。 您可以在虛擬網路中的內部部署機器或虛擬機器上安裝自我裝載的 Integration Runtime，以便在雲端資料存放區與私人網路中的資料存放區之間執行複製活動，或是對內部部署網路或 Azure 虛擬網路中的計算資源分派轉換活動。 

Data Factory 與客戶虛擬網路之間必須有數個通道。


| **網域** | **連接埠** | **說明** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | 控制平面。 Data Factory 撰寫和監視所需。 |
| `*.{region}.datafactory.azure.net` | 443 | 自我裝載 Integration Runtime 所需，以連接到 Data Factory 服務。 |
| `*.servicebus.windows.net` | 443 | 自我裝載 Integration Runtime 所需的互動式撰寫。 |
| `download.microsoft.com` | 443 | 自我裝載 Integration Runtime 用來下載更新的必要項。 |


藉由支援 Azure Data Factory 的 Azure Private Link，您可以在虛擬網路中建立私人端點 (PE) ，並啟用特定 Azure Data Factory 的私人連線。 Azure Data Factory 服務的通訊會透過 Azure Private Link 提供安全的私用連線能力。 此外，您不需要在虛擬網路或公司防火牆中設定上述的網域和埠，以提供更安全的方式來保護您的資源。  

![Azure Data Factory Private Link 架構](./media/data-factory-private-link/private-link-architecture.png)

以下是針對以上所述的每個通道啟用 Private Link 服務的優點：
-  (支援) 您可以在虛擬網路中撰寫和監視 Azure Data Factory，即使您封鎖所有輸出通訊。
-  (支援) 自我裝載 Integration Runtime 和 Azure Data Factory 服務之間的命令通訊，可在私人網路環境中安全地執行。 自我裝載的 Integration Runtime 與 Azure Data Factory 服務之間的流量會經過 Private Link。 
-  (目前不支援使用自我裝載 Integration Runtime) 互動式撰寫，例如測試 Private Link 連線、流覽資料夾清單和資料表清單、取得架構和預覽資料。
- 目前不支援 () 如果您啟用自動更新，就可以從下載中心自動下載新版的自我裝載 Integration Runtime。

> [!NOTE]
> 針對目前不支援的功能，您仍然需要在虛擬網路或公司防火牆中設定上述的網域和埠。 

> [!WARNING]
> 當您建立連結服務時，請確定認證儲存在 Azure Key Vault 中。 否則，當您在 Azure Data Factory 中啟用 Private Link 服務時，它將無法運作。

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>如何設定 Azure Data Factory 的 Private Link
您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來建立私人端點：

[入口網站](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


您也可以在 Azure 入口網站中流覽至您的 Azure Data Factory，並 (PE) 建立私人端點：

![建立私人端點](./media/data-factory-private-link/create-private-endpoint.png)


如果您想要封鎖此 Azure Data Factory 的公用存取，而且只允許透過 Private Link 的存取，您可以在 Azure 入口網站中停用 Azure Data Factory 的網路存取：

![建立私人端點](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> 停用公用網路存取只適用于自我裝載的 Integration Runtime，而不是 Azure Integration Runtime 和 SSIS Integration Runtime。

> [!NOTE]
> 停用公用網路存取之後，使用者仍然可以透過公用網路存取 Azure Data Factory 入口網站。

## <a name="next-steps"></a>接下來的步驟

- [使用 Azure Data Factory UI 來建立資料處理站](quickstart-create-data-factory-portal.md)

- [Azure Data Factory 簡介](introduction.md)

- [Azure Data Factory 中的視覺化撰寫](author-visually.md)

