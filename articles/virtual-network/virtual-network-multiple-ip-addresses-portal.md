---
title: Azure 虛擬機器的多個 IP 位址 - 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站 / Resource Manager 對虛擬機器指派多個 IP 位址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 135504ad4956836614bfc0f2cb107e610de17c0c
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543481"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>使用 Azure 入口網站將多個 IP 位址指派給虛擬機器

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> 本文說明如何使用 Azure 入口網站透過 Azure Resource Manager 部署模型建立虛擬機器 (VM)。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM

如果您想要建立具有多個 IP 位址或一個靜態私人 IP 位址的 VM，您必須使用 PowerShell 或 Azure CLI 來建立它。 若要深入了解，請按一下本文頂端的 PowerShell 或 CLI 選項。 您可以建立具有單一動態私人 IP 位址及 (選擇性) 單一公用 IP 位址的 VM。 遵循[建立 Windows VM](../virtual-machines/windows/quick-create-portal.md) 或[建立 Linux VM](../virtual-machines/linux/quick-create-portal.md) 文章中的步驟，以使用入口網站。 建立 VM 之後，您可以依照本文的[將 IP 位址新增至 VM](#add) 一節中的步驟，透過入口網站將 IP 位址類型從動態變更為靜態，並新增其他 IP 位址。

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>將 IP 位址新增至 VM

您可以完成後續步驟，將私人和公用 IP 位址新增至 Azure 網路介面。 下列各節中的範例假設您已經有一個 VM，其具有[案例](#scenario)中所述的三項 IP 組態，但這不是必要項目。

### <a name="core-steps"></a><a name="coreadd"></a>核心步驟

1. 瀏覽至 Azure 入口網站 (https://portal.azure.com) 並視需要進行登入。
2. 在 Azure 入口網站中，按一下 [更多服務]****，接著在篩選方塊中輸入「虛擬機器」**，然後按一下 [虛擬機器]****。
3. 在 [虛擬機器]**** 窗格中，按一下您想要新增 IP 位址的 VM。 流覽至 [**網路**功能] 索引標籤。按一下頁面上的 [**網路介面**]。 如下圖所示： 


    ![在 VM 中新增公用 IP 位址](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. 在 [**網路介面**] 窗格中，按一下 [ **IP**設定]。

5. 在針對所選 NIC 顯示的窗格中，按一下 [IP 組態]****。 按一下 [**新增**]，根據您要新增的 IP 位址類型，完成下列其中一個章節中的步驟，然後按一下 **[確定]**。 

### <a name="add-a-private-ip-address"></a>新增私人 IP 位址

完成下列步驟，以新增私人 IP 位址：

1. 完成本文的[核心步驟](#coreadd)一節中的步驟，並確定您位於 VM 網路介面的 [ **IP**設定] 區段中。  檢查顯示為預設值的子網（例如 10.0.0.0/24）。
2. 按一下 [新增] 。 在出現的 [**新增 ip**設定] 窗格中，以新的*靜態*私人 IP 位址建立名為*IPConfig-4*的 IP 設定，方法是挑選最後八位的新號碼，然後按一下 **[確定]**。  （針對 10.0.0.0/24 子網，範例 IP 會是*10.0.0.7*）。

    > [!NOTE]
    > 新增靜態 IP 位址時，您必須在 NIC 所連接的子網路上指定未使用的有效位址。 如果您選取的位址無法使用，入口網站會針對 IP 位址顯示 X，而您必須選取不同的位址。

3. 按一下 [確定] 後，窗格便會關閉，而您會看到新的 IP 組態列出。 按一下 [確定]****，以關閉 [新增 IP 組態]**** 窗格。
4. 您可以按一下 [新增]**** 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。
5. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中的步驟，將私人 IP 位址新增至 VM 作業系統。

### <a name="add-a-public-ip-address"></a>新增公用 IP 位址

將公用 IP 位址與新的 IP 組態或現有的 IP 組態產生關聯，便可新增公用 IP 位址。

> [!NOTE]
> 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)文章。
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>建立公用 IP 位址資源

公用 IP 位址是公用 IP 位址資源的其中一項設定 如果您的公用 IP 位址資源目前並未關聯至您想要產生關聯的 IP 組態，請略過下列步驟，並視需要完成後續其中一節的步驟。 如果您沒有可用的公用 IP 位址資源，請完成下列步驟來建立一個︰

1. 瀏覽至 Azure 入口網站 (https://portal.azure.com) 並視需要進行登入。
3. 在入口網站中，按一下 [**建立資源**] [網路] [  >  **Networking**  >  **公用 IP 位址**]。
4. 在顯示的 [建立公用 IP 位址]**** 窗格中，輸入 [名稱]****，選取 [IP 位址指派]**** 類型、[訂用帳戶]****、[資源群組]**** 和 [位置]****，然後按一下 [建立]****，如下圖所示︰

    ![建立公用 IP 位址資源](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. 完成後續其中一節的步驟，將公用 IP 位址資源與 IP 組態產生關聯。

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>將公用 IP 位址資源與新的 IP 組態產生關聯

1. 完成本文的[核心步驟](#coreadd)一節中的步驟。
2. 按一下 [新增] 。 在顯示的 [新增 IP 組態]**** 窗格中，建立名為 IPConfig-4** 的 IP 組態。 啟用 [公用 IP 位址]****，然後從顯示的 [選擇公用 IP 位址]**** 窗格中，選取現有的可用公用 IP 位址資源。

    選取公用 IP 位址資源後，請按一下 [確定]****，窗格便會關閉。 如果您沒有現有的公用 IP 位址，您可以完成[建立公用 IP 位址資源](#create-public-ip)一節中的步驟，加以建立。 

3. 檢閱新的 IP 組態。 即使未明確指派私人 IP 位址，但已自動指派一個給 IP 組態，因為所有 IP 組態都必須有一個私人 IP 位址。
4. 您可以按一下 [新增]**** 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。
5. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。 請勿將公用 IP 位址新增至作業系統。

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>將公用 IP 位址資源與現有的 IP 組態產生關聯

1. 完成本文的[核心步驟](#coreadd)一節中的步驟。
2. 按一下您想要新增公用 IP 位址資源的 IP 組態。
3. 在顯示的 [IPConfig] 窗格中，按一下 [IP 位址]****。
4. 在顯示的 [選擇公用 IP 位址]**** 窗格中，選取一個公用 IP 位址。
5. 按一下 [儲存]****，這些窗格便會關閉。 如果您沒有現有的公用 IP 位址，您可以完成[建立公用 IP 位址資源](#create-public-ip)一節中的步驟，加以建立。
3. 檢閱新的 IP 組態。
4. 您可以按一下 [新增]**** 來新增其他 IP 組態，或關閉所有開啟的刀鋒視窗以完成 IP 位址新增。 請勿將公用 IP 位址新增至作業系統。


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
