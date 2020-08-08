---
title: 部署 Windows 7 虛擬機器 Windows 虛擬桌面 (傳統) -Azure
description: 如何在 Windows 虛擬桌面 Windows 虛擬桌面 (傳統) 上設定和部署 Windows 7 虛擬機器。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 259e49fbdd6a0eb392ddf6a3cd3c318798cfabd0
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005056"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>在 Windows 虛擬桌面 (傳統) 上部署 Windows 7 虛擬機器

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理目前版本 Windows 虛擬桌面中引進 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../deploy-windows-7-virtual-machine.md)。

在 Windows 虛擬桌面上部署 Windows 7 虛擬機器 (VM) 的程式，與執行較新版本 Windows 的 Vm 略有不同。 本指南將告訴您如何部署 Windows 7。

## <a name="prerequisites"></a>必要條件

開始之前，請遵循[使用 PowerShell 建立主機集](create-host-pools-powershell-2019.md)區中的指示來建立主機集區。 之後，請依照在 Azure Marketplace 中[建立主機](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group)集區中的指示，將一或多個使用者指派給桌面應用程式群組。

## <a name="configure-a-windows-7-virtual-machine"></a>設定 Windows 7 虛擬機器

完成必要條件之後，您就可以設定要在 Windows 虛擬桌面上部署的 Windows 7 VM。

若要在 Windows 虛擬桌面上設定 Windows 7 VM：

1. 登入 Azure 入口網站並搜尋 Windows 7 Enterprise 映射，或上傳您自己自訂的 Windows 7 Enterprise (x64) 映射。
2. 以 Windows 7 Enterprise 作為其主機作業系統，部署一或多部虛擬機器。 請確定虛擬機器允許遠端桌面通訊協定 (RDP)  (TCP/3389 埠) 。
3. 使用 RDP 連接到 Windows 7 企業版主機，並使用您在設定部署時所定義的認證進行驗證。
4. 將使用 RDP 連接到主機時所用的帳戶新增至「遠端桌面使用者」群組。 如果您不這麼做，在將 VM 加入 Active Directory 網域之後，您可能無法連線到該 VM。
5. 移至 VM 上的 Windows Update。
6. 安裝 [重要] 分類中的所有 Windows 更新。
7. 在 [選用類別] 中安裝所有 Windows 更新， (排除語言套件) 。 這會安裝您完成這些指示所需的遠端桌面通訊協定8.0 更新 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) 。
8. 開啟本機群組原則編輯器，然後流覽至 [**電腦**設定  >  **系統管理範本**  >  **Windows 元件**]  >  **遠端桌面服務**  >  **遠端桌面工作階段主機**  >  **遠端會話環境**]。
9. 啟用遠端桌面通訊協定8.0 原則。
10. 將此 VM 加入您的 Active Directory 網域。
11. 執行下列命令來重新開機虛擬機器：

     ```cmd
     shutdown /r /t 0
     ```

12. 依照[這裡](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/)的指示來取得註冊權杖。
13. [下載適用于 Windows 7 的 Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下載適用于 Windows 7 的 Windows 虛擬桌面代理程式管理員](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 開啟 Windows 虛擬桌面 Agent 安裝程式，並遵循指示進行。 出現提示時，提供您在步驟12中建立的註冊金鑰。
16. 開啟 Windows 虛擬桌面代理程式管理員，並遵循指示進行。
17. （選擇性）封鎖 TCP/3389 埠，以移除對 VM 的直接遠端桌面通訊協定存取。
18. （選擇性）確認您的 .NET framework 至少為版本4.7.2。 如果您要建立自訂映射，這一點特別重要。

## <a name="next-steps"></a>後續步驟

您的 Windows 虛擬桌面部署現在已可供使用。 [下載最新版本的 Windows 虛擬桌面用戶端](https://aka.ms/wvd/clients/windows)以開始使用。

如需 Windows 虛擬桌面上 Windows 7 的已知問題和疑難排解指示清單，請參閱疑難排解[Windows 虛擬桌面中的 windows 7 虛擬機器](troubleshoot-windows-7-vm.md)疑難排解一文。
