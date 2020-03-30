---
title: Windows 虛擬桌面主機池 Azure 資源管理器 - Azure
description: 如何使用 Azure 資源管理器範本在 Windows 虛擬桌面中創建主機池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292335"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立主機集區

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

按照本節的說明，使用 Microsoft 提供的 Azure 資源管理器範本為 Windows 虛擬桌面租戶創建主機池。 本文將介紹如何在 Windows 虛擬桌面中創建主機池、在 Azure 訂閱中使用 VM 創建資源組、將這些 VM 加入 AD 域以及向 Windows 虛擬桌面註冊 VM。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>執行 Azure Resource Manager 範本需要什麼

在運行 Azure 資源管理器範本之前，請確保知道以下事項：

- 要使用的圖像源位於何處。 它是從 Azure 庫中更新還是自訂？
- 您的域加入憑據。
- 您的 Windows 虛擬桌面憑據。

使用 Azure 資源管理器範本創建 Windows 虛擬桌面主機池時，可以從 Azure 庫、託管映射或非託管映射創建虛擬機器。 要瞭解有關如何創建 VM 映射的資訊，請參閱[準備 Windows VHD 或 VHDX 以上載到 Azure，](../virtual-machines/windows/prepare-for-upload-vhd-image.md)並在[Azure 中創建通用 VM 的託管映射](../virtual-machines/windows/capture-image-resource.md)。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>運行 Azure 資源管理器範本以預配新的主機池

要開始，轉到[此 GitHub URL。](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)

### <a name="deploy-the-template-to-azure"></a>將範本部署到 Azure

如果要在企業版訂閱中部署，請向下滾動並選擇"**部署到 Azure"，** 然後跳過前面的基於映射源填寫參數。

如果要在雲解決方案提供程式訂閱中部署，請按照以下步驟部署到 Azure：

1. 向下捲動並以滑鼠右鍵按一下 [部署至 Azure]****，然後選取 [複製連結位置]****。
2. 開啟文字編輯器 (例如 [記事本])，並在該處貼上連結。
3. 在""https://portal.azure.com/之後和井號標籤 （#） 輸入一個在符號 （+） 後跟租戶功能變數名稱之前。 下面是您應該使用的格式的示例： `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`。
4. 以具有「雲端解決方案提供者」訂用帳戶系統管理員/參與者權限的使用者身分登入 Azure 入口網站。
5. 將您之前複製到文字編輯器的連結貼到網址列。

有關應為方案輸入哪些參數的指導，請參閱 Windows 虛擬桌面[Readme 檔](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)。 Readme 始終使用最新更改進行更新。

## <a name="assign-users-to-the-desktop-application-group"></a>將使用者分配到桌面應用程式組

GitHub Azure 資源管理器範本完成後，在開始測試虛擬機器上的完整會話桌面之前，分配使用者存取權限。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

要將使用者分配到桌面應用程式組，請打開 PowerShell 視窗並運行此 Cmdlet 以登錄到 Windows 虛擬桌面環境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

之後，使用以下 Cmdlet 將使用者添加到桌面應用程式組：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

使用者的 UPN 應與 Azure 活動目錄中的使用者標識匹配（例如， user1@contoso.com 如果您想要新增多個使用者，則必須針對每個使用者執行此 Cmdlet。

在您完成這些步驟之後，新增至桌面應用程式群組的使用者可以透過支援的遠端桌面用戶端登入 Windows 虛擬桌面，並看到工作階段桌面的資源。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../security-center/security-center-just-in-time.md)。