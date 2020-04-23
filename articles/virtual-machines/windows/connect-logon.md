---
title: 連接到 Windows 伺服器 VM
description: 了解如何使用 Azure 入口網站和 Resource Manager 部署模型來連接和登入 Windows VM。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 92f7ba30e2a3c66218974ab65502c35139ab1f8c
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086210"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>如何連接和登入執行 Windows 的 Azure 虛擬機器
您會使用 Azure 入口網站中的 [連線]**** 按鈕，從 Windows 桌面啟動遠端桌面 (RDP) 工作階段。 首先您必須連線到虛擬機器，然後登入。

若要從 Mac 連線到 Windows VM，您需要安裝適用於 Mac 的 RDP 用戶端，例如 [Microsoft 遠端桌面](https://aka.ms/rdmac)。

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器
1. 轉到[Azure 門戶](https://portal.azure.com/)以連接到 VM。 搜尋並選取 [虛擬機器]  。
2. 然後從清單中選取虛擬機器。
3. 在虛擬機頁面的開頭,選擇 **「連接**」。
4. 在 **'連線到虛擬機器'** 頁上,選擇**RDP,** 然後選擇相關的**IP 位址**與**連接埠號**。 在大部分情況下，就應該使用預設 IP 位址和連接埠。 選取 [下載 RDP 檔案]  。 如果 VM 已設定 Just-In-Time 原則，您必須先選取 [要求存取]**** 按鈕來要求存取，才能下載 RDP 檔案。 如需 Just-In-Time 原則的詳細資訊，請參閱[使用 Just-In-Time 原則管理虛擬機器存取](../../security-center/security-center-just-in-time.md)。
5. 開啟下載的 RDP 檔案，然後在出現提示時選取 [連線]****。 您會收到警告，表示 `.rdp` 檔案來自未知的發行者。 這是預期行為。 在 [遠端桌面連線]**** 視窗中，選取 [連線]**** 以繼續。
   
    ![未知發行者相關警告的螢幕擷取畫面。](./media/connect-logon/rdp-warn.png)
3. 在 [Windows 安全性]  視窗中，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 輸入虛擬機器上帳戶的認證，然後選取 [確定]****。
   
     **本機帳戶**：這通常是您在建立虛擬機器時所指定的本機帳戶使用者名稱與密碼。 在此案例中，虛擬機器的名稱是網域，而它的輸入格式為 *vmname*&#92;*username*。  
   
    **已加入網域的 VM**：如果 VM 屬於某個網域，請以 *Domain*&#92;*Username* 格式輸入使用者名稱。 此帳戶也必須屬於系統管理員群組，或者已授與遠端存取 VM 的權限。
   
    **網域控制站**：如果 VM 是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱與密碼。
4. 選取 [是]**** 以確認虛擬機器的身分識別，並完成登入。
   
   ![顯示驗證 VM 身分識別相關訊息的螢幕擷取畫面。](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > 如果入口網站中的 [連線]**** 按鈕呈現灰色，而且您未透過 [Express Route](../../expressroute/expressroute-introduction.md) 或[站台對站台 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 連線來連線到 Azure，您必須先建立 VM 並為它指派公用 IP 位址，才能使用 RDP。 如需詳細資訊，請參閱 [Azure 中的公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>使用 PowerShell 連線到虛擬機器

 

如果您正在使用 PowerShell 並安裝了 Azure PowerShell`Get-AzRemoteDesktopFile`模組,也可以使用 cmdlet 進行連接,如下所示。

此範例會立即啟動 RDP 連線，帶您進行類似上面的提示。

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

您也可以儲存 RDP 檔案供日後使用。

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>後續步驟
如果您無法連線，請參閱[針對遠端桌面連線進行疑難排解](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

