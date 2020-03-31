---
title: 使用 Azure 堡壘連接到 Windows 虛擬機器規模集 |微軟文檔
description: 在本文中，瞭解如何使用 Azure 堡壘連接到 Azure 虛擬機器縮放集。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988085"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>使用 Azure 堡壘連接到虛擬機器規模集

本文演示如何使用 Azure 堡壘在 Azure 虛擬網路中安全無縫地將 RDP 到 Windows 虛擬機器縮放集實例。 可以直接從 Azure 門戶連接到虛擬機器縮放集實例。 使用 Azure 堡壘時，VM 不需要用戶端、代理或其他軟體。 有關 Azure 堡壘的詳細資訊，請參閱[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>開始之前

請確保已為虛擬機器縮放集所在的虛擬網路設置了 Azure 堡壘主機。 有關詳細資訊，請參閱創建[Azure 堡壘主機](bastion-create-host-portal.md)。 在虛擬網路中預配和部署 Bastion 服務後，可以使用它連接到此虛擬網路中的虛擬機器規模集實例。 Bastion 假定您正在使用 RDP 連接到 Windows 虛擬機器規模集，SSH 連接到 Linux 虛擬機器規模集。 有關連接到 Linux VM 的資訊，請參閱[連接到 VM - Linux](bastion-connect-vm-ssh.md)。

## <a name="connect-using-rdp"></a><a name="rdp"></a>使用 RDP 連接

1. 打開[Azure 門戶](https://portal.azure.com)。 導航到要連接到的虛擬機器縮放集。

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. 導航到要連接到的虛擬機器縮放集實例，然後選擇 **"連接**"。 使用 RDP 連接時，虛擬機器縮放集應為 Windows 虛擬機器縮放集。

   ![虛擬機器規模集](./media/bastion-connect-vm-scale-set/2.png)
3. 選擇 **"連接"** 後，將顯示一個側欄，該邊欄具有三個選項卡 - RDP、SSH 和堡壘。 從側欄中選擇 **"堡壘**"選項卡。 如果未為虛擬網路預配堡壘，則可以選擇用於配置堡壘的連結。 有關配置說明，請參閱[配置堡壘](bastion-create-host-portal.md)。

   ![堡壘選項卡](./media/bastion-connect-vm-scale-set/3.png)
4. 在"堡壘"選項卡上，輸入虛擬機器規模集的使用者名和密碼，然後選擇"**連接**"。

   ![連線](./media/bastion-connect-vm-scale-set/4.png)
5. 通過 Bastion 連接到此虛擬機器的 RDP 連接將直接在 Azure 門戶（通過 HTML5）中使用埠 443 和堡壘服務打開。

## <a name="next-steps"></a>後續步驟

閱讀[堡壘常見問題解答](bastion-faq.md)。