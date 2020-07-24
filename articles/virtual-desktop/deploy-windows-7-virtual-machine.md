---
title: 部署 Windows 7 虛擬機器 Windows 虛擬桌面春季 2020-Azure
description: 如何在 Windows 虛擬桌面上設定和部署 Windows 7 虛擬機器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b589890f7b65b41cf6b7ba4fdf53b71173ed6a38
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020435"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面上部署 Windows 7 虛擬機器

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Windows 虛擬桌面上部署 Windows 7 虛擬機器（VM）的程式，與執行較新版本 Windows 的 Vm 略有不同。 本指南將告訴您如何部署 Windows 7。

## <a name="prerequisites"></a>先決條件

開始之前，請遵循[使用 PowerShell 建立主機集](create-host-pools-powershell.md)區中的指示來建立主機集區。 如果您使用的是入口網站，請依照[使用 Azure 入口網站建立主機集](create-host-pools-azure-marketplace.md)區的步驟1到9中的指示進行。 之後，選取 [**審查 + 建立**] 以建立空的主機集區。 

## <a name="configure-a-windows-7-virtual-machine"></a>設定 Windows 7 虛擬機器

完成必要條件之後，您就可以設定要在 Windows 虛擬桌面上部署的 Windows 7 VM。

若要在 Windows 虛擬桌面上設定 Windows 7 VM：

1. 登入 Azure 入口網站並搜尋 Windows 7 Enterprise 映射，或上傳您自己的自訂 Windows 7 Enterprise （x64）映射。
2. 以 Windows 7 Enterprise 作為其主機作業系統，部署一或多部虛擬機器。 請確定虛擬機器允許遠端桌面通訊協定（RDP）（TCP/3389 埠）。
3. 使用 RDP 連接到 Windows 7 企業版主機，並使用您在設定部署時所定義的認證進行驗證。
4. 將使用 RDP 連接到主機時所用的帳戶新增至「遠端桌面使用者」群組。 如果您未新增帳戶，您可能無法在將其加入至 Active Directory 網域之後連線到 VM。
5. 移至 VM 上的 Windows Update。
6. 安裝 [重要] 分類中的所有 Windows 更新。
7. 安裝選用類別（不含語言套件）中的所有 Windows 更新。 此程式會安裝完成這些指示所需的遠端桌面通訊協定8.0 更新（[KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)）。
8. 開啟本機群組原則編輯器，然後流覽至 [**電腦**設定  >  **系統管理範本**  >  **Windows 元件**]  >  **遠端桌面服務**  >  **遠端桌面工作階段主機**  >  **遠端會話環境**]。
9. 啟用遠端桌面通訊協定8.0 原則。
10. 將此 VM 加入您的 Active Directory 網域。
11. 執行下列命令來重新開機虛擬機器：

     ```cmd
     shutdown /r /t 0
     ```

12. 依照[這裡](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo?view=azps-4.3.0)的指示來取得註冊權杖。
      
      - 如果您想要使用 Azure 入口網站，您也可以移至您想要新增 VM 的主機集區的 [總覽] 頁面，並在該處建立權杖。
  
13. [下載適用于 Windows 7 的 Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下載適用于 Windows 7 的 Windows 虛擬桌面代理程式管理員](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 開啟 Windows 虛擬桌面 Agent 安裝程式，並遵循指示進行。 出現提示時，提供您在步驟12中建立的註冊金鑰。
16. 開啟 Windows 虛擬桌面代理程式管理員，並遵循指示進行。
17. （選擇性）封鎖 TCP/3389 埠，以移除對 VM 的直接遠端桌面通訊協定存取。
18. （選擇性）確認您的 .NET framework 至少為版本4.7.2。 如果您要建立自訂映射，則更新您的架構特別重要。

## <a name="next-steps"></a>接下來的步驟

您的 Windows 虛擬桌面部署現在已可供使用。 [下載最新版本的 Windows 虛擬桌面用戶端](https://aka.ms/wvd/clients/windows)以開始使用。

如需 Windows 虛擬桌面上 Windows 7 的已知問題和疑難排解指示清單，請參閱疑難排解[Windows 虛擬桌面中的 windows 7 虛擬機器](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)疑難排解一文。
