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
ms.openlocfilehash: a6faac66edaaf047f5ba025e94a1522c2313f9ed
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546665"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory 的 Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

藉由使用 Azure Private Link，您可以透過私人端點，連接到 Azure 中的各種平臺即服務 (PaaS) 部署。 私人端點是特定虛擬網路和子網內的私人 IP 位址。 如需支援 Private Link 功能的 PaaS 部署清單，請參閱 [Private Link 檔](https://docs.microsoft.com/azure/private-link/)。 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>在客戶網路與 Azure Data Factory 之間進行安全通訊 
您可以將 Azure 虛擬網路設定為雲端中網路的邏輯標記法。 這麼做可提供下列優點：
* 您可以協助保護您的 Azure 資源免于遭受公用網路中的攻擊。
* 您可以讓網路和 Data Factory 安全地彼此通訊。 

您也可以藉由設定網際網路通訊協定安全性 (IPsec) VPN (站對站) 連線或 Azure ExpressRoute (私人對等互連) 連線，將內部部署網路連線到您的虛擬網路。 

您也可以在內部部署機器或虛擬網路中的虛擬機器上安裝自我裝載整合執行時間。 這樣做可讓您：
* 在雲端資料存放區與私人網路中的資料存放區之間執行複製活動。
* 針對內部部署網路或 Azure 虛擬網路中的計算資源分派轉換活動。 

Azure Data Factory 與客戶虛擬網路之間必須有數個通道，如下表所示：

| 網域 | 連接埠 | 描述 |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory 撰寫和監視所需的控制平面。 |
| `*.{region}.datafactory.azure.net` | 443 | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 |
| `*.servicebus.windows.net` | 443 | 自我裝載整合執行時間所需的互動式撰寫。 |
| `download.microsoft.com` | 443 | 自我裝載整合執行階段所需，以用於下載更新。 |

由於支援 Azure Data Factory 的 Private Link，您可以：
* 在您的虛擬網路中建立私人端點。
* 啟用特定 data factory 實例的私用連接。 

Azure Data Factory 服務的通訊會通過 Private Link 並協助提供安全的私用連線能力。 您不需要在虛擬網路或公司防火牆中設定上述的網域和埠，即可提供更安全的方式來保護您的資源。  

![Azure Data Factory 架構的 Private Link 圖表。](./media/data-factory-private-link/private-link-architecture.png)

針對上述每個通道啟用 Private Link 服務可提供下列功能：
- **支援** ：
   - 即使您封鎖所有輸出通訊，您也可以在虛擬網路中撰寫和監視資料處理站。
   - 自我裝載整合執行時間和 Azure Data Factory 服務之間的命令通訊可以安全地在私人網路環境中執行。 自我裝載整合執行時間和 Azure Data Factory 服務之間的流量會經歷 Private Link。 
- **目前不支援** ：
   - 使用自我裝載整合執行時間（例如測試連線、流覽資料夾清單和資料表清單、取得架構和預覽資料）的互動式撰寫會經歷 Private Link。
   - 如果您啟用自動更新，則可以從 Microsoft 下載中心自動下載新版的自我裝載整合執行時間。

   > [!NOTE]
   > 針對目前不支援的功能，您仍然需要在虛擬網路或公司防火牆中設定先前提及的網域和埠。 

> [!WARNING]
> 當您建立連結服務時，請確定您的認證儲存在 Azure key vault 中。 否則，當您在 Azure Data Factory 中啟用 Private Link 時，認證將無法運作。

## <a name="set-up-private-link-for-azure-data-factory"></a>設定 Azure Data Factory 的 Private Link
您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)來建立私人端點。

您也可以移至 Azure 入口網站中的 Azure data factory，並建立私人端點，如下所示：

![用來建立私人端點的 [私人端點連線] 窗格螢幕擷取畫面。](./media/data-factory-private-link/create-private-endpoint.png)


如果您想要封鎖對 Azure data factory 的公用存取，而且只允許透過 Private Link 存取，請停用 Azure 入口網站中 Azure Data Factory 的網路存取，如下所示：

![用來建立私人端點的 [網路存取] 窗格螢幕擷取畫面。](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> 停用公用網路存取只適用于自我裝載整合執行時間，而不是 Azure Integration Runtime 和 SQL Server Integration Services (SSIS) Integration Runtime。

> [!NOTE]
> 您仍然可以在停用公用網路存取之後，透過公用網路存取 Azure Data Factory 入口網站。

## <a name="next-steps"></a>下一步

- [使用 Azure Data Factory UI 來建立資料處理站](quickstart-create-data-factory-portal.md)
- [Azure Data Factory 簡介](introduction.md)
- [Azure Data Factory 中的視覺化撰寫](author-visually.md)

