---
title: 連接到基於 Microsoft Azure 的虛擬機 |Azure 應用商店
description: 說明如何連線到在 Azure 上建立的新虛擬機器。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6f2652a98aa081b62ba80951212ef3a3de513593
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272656"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>連線至您的 Azure 型虛擬機器

> [!IMPORTANT]
> 從 2020 年 4 月 13 日開始,我們將開始向合作夥伴中心移動 Azure 虛擬機器產品/ 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照創建 Azure[虛擬機器技術資產](https://aka.ms/AzureVMTechAsset)中的說明進行操作,以管理遷移的優惠。

本文將說明如何連線至並登入您在 Azure 建立的虛擬機器 (VM)。  成功連接後,可以使用 VM,就像您本地登錄到其主機伺服器一樣。 

## <a name="connect-to-a-windows-based-vm"></a>連線至 Windows 型 VM

您將使用遠端桌面用戶端連接到 Azure 上託管的基於 Windows 的 VM。  大部分的 Windows 版本原本就支援遠端桌面通訊協定 (RDP)。  如需其他機器的用戶端資訊，請參閱[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。  

下列文章詳細說明如何使用內建的 Windows RDP 支援服務連線至您的 VM：[如何連線並登入搭載 Windows 的 Azure 虛擬機器](../../../virtual-machines/windows/connect-logon.md)。  

>[!TIP]
> 您可能會在過程中收到安全性警告，例如 .rdp 檔案來自未知的發行者，或使用者的認證無法驗證。  您可以放心忽略這些警告。


## <a name="connect-to-a-linux-based-vm"></a>連線至 Linux 型 VM

要連接基於 Linux 的 VM,您需要一個安全的 shell 協定 (SSH) 用戶端。  本討論會使用免費的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 終端機。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com)。 搜尋並選擇**虛擬機器**。 
2. 選擇要連接到的 VM。  
3. 如果 VM 尚未運行,則**啟動**它。
4. 按一下 VM 的名稱，以開啟其**概觀**頁面。
5. 請記下您 VM 的公用 IP 位址和 DNS 名稱。  (如果尚未設定這些值，則必須[建立網路介面](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![VM 概觀設定](./media/publishvm_019.png)
 
6. 開啟 PuTTY 應用程式。  
7. 在 [PuTTY 設定] 對話方塊中，輸入您 VM 的 IP 位址或 DNS 名稱。 

   ![PuTTY 終端機設定](./media/publishvm_020.png)
 
8. 按一下**開啟**以開啟 PuTTY 終端機。  
9. 系統提示您,輸入 Linux VM 帳戶的帳戶名稱和密碼。 

如有連線問題，請參閱 SSH 用戶端的文件，例如[第 10 章：常見錯誤訊息](https://www.ssh.com/ssh/putty/putty-manuals)。

如需詳細資訊，包括如何將桌面新增至佈建的 Linux VM，請參閱[安裝和設定遠端桌面以在 Azure 中連線至 Linux VM](../../../virtual-machines/linux/use-remote-desktop.md)。


## <a name="stop-unused-vms"></a>停止未使用的 VM
無論 VM 為執行中*或閒置狀態*，Azure 將收取 VM 裝載費用。  因此,最佳做法是停止當前未使用的 VM。  例如，建議您停止測試、備分或已淘汰 VM。 要關閉 VM,請完成以下步驟:

1. 在**虛擬機器**刀鋒視窗中，選取您要停止的 VM。 
2. 在靠近頁面頂端的工具列中，按一下**停止**按鈕。

   ![停止 VM](./media/publishvm_018.png)

Azure 會在 *deallocation* 流程中快速停止 VM，除了停止 VM 上的作業系統之外，也會釋出原先佈建的硬體和網路資源。

如果您想要稍後重新啟動已停止的 VM，請選取該 VM，然後按一下**啟動**按鈕。


## <a name="next-steps"></a>後續步驟

遠端連接後,即可[配置 VM。](./cpp-configure-vm.md)
