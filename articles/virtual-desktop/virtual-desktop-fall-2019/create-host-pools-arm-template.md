---
title: Windows 虛擬桌面主機集區 Azure Resource Manager-Azure
description: 如何使用 Azure Resource Manager 範本在 Windows 虛擬桌面中建立主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c24e212f901f0af38a24ab203070f637de860fc7
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615184"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立主機集區

>[!IMPORTANT]
>此內容適用于不支援 Azure Resource Manager Windows 虛擬桌面物件的秋季2019版。

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

遵循本節的指示，使用 Microsoft 提供的 Azure Resource Manager 範本來建立 Windows 虛擬桌面租使用者的主機集區。 本文將告訴您如何在 Windows 虛擬桌面中建立主機集區、使用 Azure 訂用帳戶中的 Vm 建立資源群組、將這些 Vm 加入 AD 網域，以及向 Windows 虛擬桌上型電腦註冊 Vm。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>執行 Azure Resource Manager 範本需要什麼

執行 Azure Resource Manager 範本之前，請確定您知道下列事項：

- 您想要使用的映射來源是。 這是來自 Azure 資源庫或它是自訂的嗎？
- 您的網域加入認證。
- 您的 Windows 虛擬桌面認證。

當您使用 Azure Resource Manager 範本建立 Windows 虛擬桌面主機集區時，您可以從 Azure 資源庫、受控映射或非受控映射建立虛擬機器。 若要深入瞭解如何建立 VM 映射，請參閱[準備 WINDOWS VHD 或 VHDX 以上傳至 azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) ，並[在 Azure 中建立一般化 VM 的受控映射](../../virtual-machines/windows/capture-image-resource.md)。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>執行 Azure Resource Manager 範本以布建新的主機集區

若要開始，請移至[此 GITHUB URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)。

### <a name="deploy-the-template-to-azure"></a>將範本部署到 Azure

如果您要在企業訂用帳戶中進行部署，請向下選取 [**部署至 Azure**]，然後跳過 [根據您的影像來源，直接填寫參數]。

如果您要在雲端解決方案提供者訂用帳戶中部署，請遵循下列步驟來部署至 Azure：

1. 向下捲動並以滑鼠右鍵按一下 [部署至 Azure]****，然後選取 [複製連結位置]****。
2. 開啟文字編輯器 (例如 [記事本])，並在該處貼上連結。
3. 緊接在 "https://portal.azure.com/" 之後，在主題標籤（#）前面輸入 @ 符號，後面接著租使用者功能變數名稱。 以下是您應該使用的格式範例： `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`。
4. 以具有「雲端解決方案提供者」訂用帳戶系統管理員/參與者權限的使用者身分登入 Azure 入口網站。
5. 將您之前複製到文字編輯器的連結貼到網址列。

如需您應該針對案例輸入哪些參數的指引，請參閱 Windows 虛擬桌面[讀我檔案](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)。 讀我檔案一律會以最新的變更進行更新。

## <a name="assign-users-to-the-desktop-application-group"></a>將使用者指派給桌面應用程式群組

GitHub Azure Resource Manager 範本完成後，在您開始測試虛擬機器上的完整會話桌面之前，請先指派使用者存取權。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

若要將使用者指派給桌面應用程式群組，請開啟 PowerShell 視窗，然後執行這個指令程式以登入 Windows 虛擬桌面環境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

之後，使用此 Cmdlet 將使用者新增至桌面應用程式群組：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

使用者的 UPN 應符合使用者在 Azure Active Directory 中的身分識別（例如user1@contoso.com）。 如果您想要新增多個使用者，則必須針對每個使用者執行此 Cmdlet。

在您完成這些步驟之後，新增至桌面應用程式群組的使用者可以透過支援的遠端桌面用戶端登入 Windows 虛擬桌面，並看到工作階段桌面的資源。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../../security-center/security-center-just-in-time.md)。