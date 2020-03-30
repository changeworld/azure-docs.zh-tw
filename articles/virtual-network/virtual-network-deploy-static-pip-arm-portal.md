---
title: 建立具有靜態公用 IP 位址的 VM - Azure 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站建立具有靜態公用 IP 位址的 VM。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043534"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>使用 Azure 入口網站建立具有靜態公用 IP 位址的虛擬機器

您可以建立具有靜態公用 IP 位址的虛擬機器。 公用 IP 位址可讓您從網際網路與虛擬機器通訊。 指派靜態公用 IP 位址 (而非動態位址)，以確保位址永遠不會變更。 深入了解[靜態公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要將指派給現有虛擬機器的公用 IP 位址從動態變更為靜態，或要處理私人 IP 位址，請參閱[新增、變更或移除 IP 位址](virtual-network-network-interface-addresses.md)。 公用 IP 位址有[象徵性費用](https://azure.microsoft.com/pricing/details/ip-addresses)，而每個訂用帳戶可用的公用 IP 位址數目都有[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 選取 Azure 入口網站左上角的 [+ 建立資源]****。
2. 選取 [計算]****，然後選取 [Windows Server 2016 VM]**** 或您所選的另一個作業系統。
3. 輸入或選擇以下資訊，接受其餘設置的預設值，然後選擇 **"確定**" ：

    |設定|值|
    |---|---|
    |名稱|myVM|
    |[使用者名稱]| 輸入您選擇的使用者名稱。|
    |密碼| 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |訂用帳戶| 選取您的訂用帳戶。|
    |資源群組| 選取 [使用現有項目]****，然後選取 [myResourceGroup]****。|
    |Location| 選擇**美國東部**|

4. 選取 VM 的大小，然後選取 [選取]****。
5. 在 [設定]**** 之下，選取 [公用 IP 位址]****。
6. 輸入 *myPublicIpAddress*，選取 [靜態]****，然後選取 [確定]****，如下圖所示：

   ![選取靜態](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   如果公用 IP 位址必須是標準 SKU，請在 [SKU]**** 之下選取 [標準]****。 深入了解[公用 IP 位址 SKU](virtual-network-ip-addresses-overview-arm.md#sku)。 如果虛擬機器將會新增至公用 Azure Load Balancer 的後端集區，則虛擬機器公用 IP 位址的 SKU 必須符合負載平衡器公用 IP 位址的 SKU。 如需詳細資訊，請參閱 [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus)。

6. 在 [選取公用輸入連接埠]**** 下選取一個連接埠，或不選取任何連接埠。 選取了連接埠 3389，就能夠從網際網路遠端存取 Windows Server 虛擬機器。 對於生產工作負載，不建議從網際網路開啟連接埠 3389。

   ![選取連接埠](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. 接受其餘的預設設定，然後選取 [確定]****。
8. 在 [摘要]**** 頁面上，選取 [建立]****。 部署虛擬機器需要幾分鐘的時間。
9. 部署虛擬機器後，請在入口網站頂端的搜尋方塊中輸入 *myPublicIpAddress*。 當 **myPublicIpAddress** 出現於搜尋結果時，請選取它。
10. 您可以檢視已指派的公用 IP 位址，並將此位址指派給 **myVM** 虛擬機器，如下圖所示：

    ![檢視公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure 已從您建立虛擬機器所在區域使用的位址中指派公用 IP 位址。 您可以針對 Azure [公開](https://www.microsoft.com/download/details.aspx?id=56519)、[US Gov](https://www.microsoft.com/download/details.aspx?id=57063)、[中國](https://www.microsoft.com/download/details.aspx?id=57062)及[德國](https://www.microsoft.com/download/details.aspx?id=57064)雲端，下載範圍 (前置詞) 清單。

11. 選取 [組態]**** 以確認指派為 [靜態]****。

    ![檢視公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> 請勿修改虛擬機器的作業系統內的 IP 位址設定。 作業系統不會察覺 Azure 公用 IP 位址。 雖然您可以將私人 IP 位址設定新增至作業系統，但是建議不要這麼做 (除非有需要)，而且在閱讀[將私人 IP 位址新增至作業系統](virtual-network-network-interface-addresses.md#private)之後才能這麼做。

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組時，請將資源群組及其包含的所有資源刪除：

1. 在入口網站頂端的 [搜尋]**** 方塊中，輸入 myResourceGroup**。 當您在搜尋結果中看到 myResourceGroup**** 時，請加以選取。
2. 選取 [刪除資源群組]****。
3. 輸入 *"資源組***"以鍵入資源組名稱：** 並選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)
- 深入了解[私人 IP 位址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)，並將[靜態私人 IP 位址](virtual-network-network-interface-addresses.md#add-ip-addresses)指派給 Azure 虛擬機器
- 深入了解如何建立 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器