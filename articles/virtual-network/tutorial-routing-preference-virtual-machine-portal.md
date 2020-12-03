---
title: 設定 VM 的路由喜好設定 - Azure 入口網站
description: 了解如何使用 Azure 入口網站，建立具有靜態公用 IP 位址和路由喜好設定選擇的 VM。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: 35e4b90e9bdd52a20bf892f0784b191384f271ac
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533701"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>使用 Azure 入口網站設定 VM 的路由喜好設定

本文說明如何設定虛擬機器的路由喜好設定。 當您選擇 **網際網路** 作為路由喜好設定選項時，來自 VM 的網際網路繫結流量將會透過 ISP 網路路由傳送。 預設路由是透過 Microsoft 全球網路。

本文說明如何使用 Azure 入口網站來建立具有公用 IP 的虛擬機器，並將其設定為透過公用網際網路路由流量。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://preview.portal.azure.com/)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 選取 [計算]，然後選取 [Windows Server 2016 VM] 或您所選的另一個作業系統。
3. 輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [確定]：

    |設定|值|
    |---|---|
    |名稱|myVM|
    |[使用者名稱]| 輸入您選擇的使用者名稱。|
    |密碼| 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |訂用帳戶| 選取您的訂用帳戶。|
    |資源群組| 選取 [使用現有項目]  ，然後選取 [myResourceGroup]  。|
    |Location| 選取 [美國東部]|

4. 選取 VM 的大小，然後選取 [選取]。
5. 在 [網路] 索引標籤下，針對 **公用 IP 位址** 按一下 [建立新的]。
6. 輸入 *myPublicIpAddress*，選取 SKU 作為 **標準**，然後選取路由喜好設定為 [網際網路]，然後按 [確定]，如下圖所示：

   ![選取靜態](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. 在 [選取公用輸入連接埠] 下選取一個連接埠，或不選取任何連接埠。 選取了連接埠 3389，就能夠從網際網路遠端存取 Windows Server 虛擬機器。 對於生產工作負載，不建議從網際網路開啟連接埠 3389。

   ![選取連接埠](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. 接受其餘的預設設定，然後選取 [確定]。
8. 在 [摘要] 頁面上，選取 [建立]。 部署虛擬機器需要幾分鐘的時間。
9. 部署虛擬機器後，請在入口網站頂端的搜尋方塊中輸入 *myPublicIpAddress*。 當 **myPublicIpAddress** 出現於搜尋結果時，請選取它。
10. 您可以檢視已指派的公用 IP 位址，並將此位址指派給 **myVM** 虛擬機器，如下圖所示：

    ![螢幕擷取畫面顯示網路介面 mynic 的 NIC 公用 I P。](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. 選取 [網路]，然後按一下 [nic mynic]，然後選取公用 IP 位址以確認路由喜好設定已指派為 [網際網路]。

    ![螢幕擷取畫面顯示公用 I P 位址的 I P 位址和路由喜好設定。](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 myResourceGroup。 當您在搜尋結果中看到 myResourceGroup  時，請加以選取。
2. 選取 [刪除資源群組]。
3. 針對 [輸入資源群組名稱:] 輸入 myResourceGroup，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟
- 深入了解[使用路由喜好設定的公用 IP](routing-preference-overview.md)。
- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。
