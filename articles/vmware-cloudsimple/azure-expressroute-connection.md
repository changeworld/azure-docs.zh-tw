---
title: Azure VMware 解決方案（按雲簡單 - 使用 ExpressRoute 將私有雲連接到 Azure 網路）
description: 介紹如何使用 ExpressRoute 將雲簡單私有雲環境連接到 Azure 虛擬網路
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015230"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>使用 ExpressRoute 將雲簡單私有雲環境連接到 Azure 虛擬網路

雲簡單私有雲可以使用 Azure ExpressRoute 連接到 Azure 虛擬網路。  這種高頻寬、低延遲連接允許您從私有雲環境訪問 Azure 訂閱中運行的服務。

虛擬網路連接允許您：

* 使用 Azure 作為私有雲上虛擬機器的備份目標。
* 在 Azure 訂閱中部署 KMS 伺服器以加密私有雲 vSAN 資料存儲。
* 使用混合應用程式，其中應用程式的 Web 層在公共雲中運行，而應用程式和資料庫層在私有雲中運行。

![Azure 快速路由連接到虛擬網路](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>設置虛擬網路連接

要設置與私有雲的虛擬網路連接，您需要授權金鑰、對等電路 URI 和對 Azure 訂閱的訪問。 此資訊可在雲簡單門戶中的虛擬網路連接頁面上找到。 有關說明，請參閱[獲取 Azure 虛擬網路到雲簡單連接的對等資訊](virtual-network-connection.md)。 如果您在獲取資訊時遇到任何問題，請提交<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援請求</a>。

> [!TIP]
> 如果您已經擁有 Azure 虛擬網路、閘道子網和虛擬網路閘道，則可以跳到步驟 4。

1. 在 Azure 訂閱上創建虛擬網路，並驗證您選擇的位址空間與私有雲的位址空間是否不同。  如果您已經擁有 Azure 虛擬網路，則可以使用現有虛擬網路。  有關詳細資訊，請參閱[使用 Azure 門戶創建虛擬網路](../virtual-network/quick-create-portal.md)。
2. 在 Azure 虛擬網路上創建閘道子網。  如果 Azure 虛擬網路中已有閘道子網，則可以使用現有子網。 有關詳細資訊，請參閱[創建閘道子網](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。
3. 在虛擬網路上創建虛擬網路閘道。  如果您有現有的虛擬網路閘道，則可以使用現有虛擬網路閘道。 有關詳細資訊，請參閱[創建虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。
4. 通過將[虛擬網路連接到電路-不同訂閱](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)中所述的授權金鑰，創建虛擬網路和私有雲之間的連接。

> [!WARNING]
> 如果您使用的是現有的虛擬網路閘道，並且它與 CloudSimple ExpressRoute 電路具有與 CloudSimple ExpressRoute 電路相同的位置的 ExpressRoute 連接，則不會建立該連接。  創建新的虛擬網路，然後按照前面的步驟操作。

## <a name="test-the-virtual-network-connection"></a>測試虛擬網路連接

創建連接後，您可以通過在 **"設置**"下選擇 **"屬性**"來檢查連接的狀態。  狀態和預配狀態應顯示**成功**。

![連線狀態](media/azure-expressroute-connection.png)

要測試虛擬網路連接，

1. 在 Azure 訂閱中創建虛擬機器。
2. 查找私有雲 vCenter 的 IP 位址（請參閱歡迎電子郵件）。
3. 從 Azure 虛擬網路中創建的虛擬機器對雲 vCenter 進行 ping。
4. 從在私有雲 vCenter 中運行的虛擬機器中對 Azure 虛擬機器進行 ping。

如果您在建立連接時出現任何問題，請提交<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援請求</a>。
