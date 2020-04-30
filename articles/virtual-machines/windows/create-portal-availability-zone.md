---
title: 使用 Azure 入口網站建立已分區的 Windows VM
description: 使用 Azure 入口網站在可用性區域中建立 Windows VM
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: b8a5f6543bc265605361b7875fad5232d428e27e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208987"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>使用 Azure 入口網站在可用性區域中建立 Windows 虛擬機器

本文逐步引導您使用 Azure 入口網站在 Azure 可用性區域中建立虛擬機器。 [可用性區域](../../availability-zones/az-overview.md)是 Azure 區域中的實體獨立區域。 萬一整個資料中心失敗或遺失，使用可用性區域可保護您的應用程式和資料免於受害。

若要使用可用性區域，請在[支援的 Azure 區域](../../availability-zones/az-region.md)中建立虛擬機器。

## <a name="sign-in-to-azure"></a>登入 Azure 

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-virtual-machine"></a>建立虛擬機器

1. 按一下 Azure 入口網站左上角的 [**建立資源**]。

2. 選取 [計算]****，然後選取 [Windows Server 2016 Datacenter]****。 

3. 輸入虛擬機器資訊。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。 選擇支援可用性區域的位置，例如美國東部 2。 完成時，按一下 **[確定]**。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. 選擇 VM 的大小。 選取建議的大小，或根據功能進行篩選。 確認該大小可在您想要使用的區域中使用。

    ![選取 VM 大小](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. 在 [**設定** > ] [**高可用性**] 底下，從 [**可用性區域**] 下拉式清單中選取其中一個編號區域，保留剩餘的預設值，然後按一下 **[確定]**。

    ![選取可用性區域](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. 在摘要頁面上，按一下 [建立]**** 開始進行虛擬機器部署。

7. 系統會將 VM 釘選到 Azure 入口網站儀表板。 一旦完成部署後，VM 摘要就會自動開啟。

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>確認受控磁碟和 IP 位址的區域

在可用性區域中部署 VM 時，會在相同的可用性區域中部署 VM 的受控磁碟。 根據預設，也會在該區域中建立公用 IP 位址。

您可以在入口網站中確認這些資源的區域設定。  

1. 按一下 [資源群組]****，然後按一下 VM 的資源群組名稱，例如 *myResourceGroup*。

2. 按一下磁碟資源的名稱。 [概觀]**** 頁面包括資源的位置和可用性區域的詳細資料。

    ![受控磁碟的可用性區域](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. 按一下公用 IP 位址資源的名稱。 [概觀]**** 頁面包括資源的位置和可用性區域的詳細資料。

    ![IP 位址的可用性區域](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>後續步驟

在本文中，您已學到如何在可用性區域中建立 VM。 深入瞭解 Azure Vm 的[可用性](availability.md)。
