---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 8de1163ab68b394b6eeaaad6412995156dbe7212
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901653"
---
## <a name="deployment-considerations"></a>部署考量因素
* **Azure 訂用帳戶** – 若要部署的不只是少數的計算密集執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

* **價格與可用性** - 這些 VM 大小僅在 [標準] 定價層提供。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/) 。 
* **核心配額** – 您可能需要從預設值增加 Azure 訂用帳戶的核心配額。 您的訂用帳戶可能也會限制您可以在特定 VM 大小系列 (包括 H 系列) 中部署的核心數目。 若要要求增加配額，可免費[開啟線上客戶支援要求](../articles/azure-portal/supportability/how-to-create-azure-support-request.md)。 (預設限制會視您的訂用帳戶類別而有所不同。)
  
  > [!NOTE]
  > 如果您有大規模的容量需求，請連絡 Azure 支援。 Azure 配額為信用額度，而不是容量保證。 無論您的配額有多少，您只需針對您使用的核心付費。
  > 
  > 
* **虛擬網路** – 使用計算密集型執行個體時，並不需要 Azure [虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/) 。 不過，您可能需要至少一個以雲端為基礎的 Azure 虛擬網路來處理許多部署，或者如果您需要存取內部部署資源，則需要站對站連線。 如有需要，請建立新的虛擬網路來部署執行個體。 不支援將計算密集型 VM 新增至同質群組中的虛擬網路。
* **調整大小** – 因為其特殊硬體，所以您只能夠在相同大小系列內重新調整計算密集型執行個體的大小 (H 系列或計算密集型 A 系列)。 例如，您只能將 H 系列 VM 的大小，從某一個 H 系列大小重新調整為另一個大小。 此外，不支援從非計算密集型大小重新調整為計算密集型大小。  

## <a name="rdma-capable-instances"></a>支援 RDMA 的執行個體
計算密集型實例（A8、A9、H16r、H16mr、HB 和 HC）的子集，功能是遠端直接記憶體存取（RDMA）連線的網路介面。 使用 ' r ' （如 NC24rs 設定，NC24rs_v2 和 NC24rs_v3）指定的 N 系列大小也可以支援 RDMA。 這是可供其他 VM 大小使用之標準 Azure 網路介面的額外界面。 
  
此介面可讓支援 RDMA 的實例透過「未使用」（IB）網路進行通訊，並以 EDR 速率針對 H16r、H16mr 和具備 RDMA 功能的 N 系列虛擬機器進行 HB、HC、FDR 速率的作業，以及 A8 和 A9 虛擬機器的 QDR 速率。 這些 RDMA 功能可以提高特定訊息傳遞介面 (MPI) 應用程式的延展性和效能。 如需速度的詳細資訊，請參閱此頁面上的表格中的詳細資料。

> [!NOTE]
> 在 Azure 中，只有在已啟用 SR-IOV 的 Vm （SR-IOV，目前為 HB 和 HC）才支援透過 IB 的 IP。 所有具備 RDMA 功能的實例都支援透過 IB 的 RDMA。
>

