---
title: 部署 Windows 7 虛擬機器 Windows 虛擬桌面-Azure
description: 如何在 Windows 虛擬桌面上設定和部署 Windows 7 虛擬機器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a367e4d98135b5d29c4a0e38363739ca2689dcab
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127985"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面上部署 Windows 7 虛擬機器

在 Windows 虛擬桌面上部署 Windows 7 虛擬機器（VM）的程式，與執行較新版本 Windows 的 Vm 略有不同。 本指南將告訴您如何部署 Windows 7。

## <a name="prerequisites"></a>Prerequisites

開始之前，請遵循[使用 PowerShell 建立主機集](create-host-pools-powershell.md)區中的指示來建立主機集區。 之後，請依照在 Azure Marketplace 中[建立主機](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group)集區中的指示，將一或多個使用者指派給桌面應用程式群組。

## <a name="configure-a-windows-7-virtual-machine"></a>設定 Windows 7 虛擬機器

完成必要條件之後，您就可以設定要在 Windows 虛擬桌面上部署的 Windows 7 VM。

若要在 Windows 虛擬桌面上設定 Windows 7 VM：

1. 登入 Azure 入口網站並搜尋 Windows 7 Enterprise 映射，或上傳您自己的自訂 Windows 7 Enterprise （x64）映射。  
2. 以 Windows 7 Enterprise 作為其主機作業系統，部署一或多部虛擬機器。 請確定虛擬機器允許遠端桌面通訊協定（RDP）（TCP/3389 埠）。
3. 使用 RDP 連接到 Windows 7 企業版主機，並使用您在設定部署時所定義的認證進行驗證。 
4. 將使用 RDP 連接到主機時所用的帳戶新增至「遠端桌面使用者」群組。 如果您不這麼做，在將 VM 加入 Active Directory 網域之後，您可能無法連線到該 VM。
5. 移至 VM 上的 Windows Update。
6. 安裝 [重要] 分類中的所有 Windows 更新。
7. 安裝選用類別（不含語言套件）中的所有 Windows 更新。 這會安裝完成這些指示所需的遠端桌面通訊協定8.0 更新（[KB2592687](https://www.microsoft.com/download/details.aspx?id=35393)）。
8. 開啟本機群組原則編輯器，然後流覽至 **電腦**設定 > **系統管理範本** > **Windows 元件** ** > 遠端桌面服務 > 遠端桌面工作階段主機** ** > ** **遠端會話環境**。
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
16. 開啟 Windows 虛擬桌面安裝程式，並遵循指示進行。
17. （選擇性）封鎖 TCP/3389 埠，以移除對 VM 的直接遠端桌面通訊協定存取。

## <a name="next-steps"></a>後續步驟

您的 Windows 虛擬桌面部署現在已可供使用。 [下載最新版本的 Windows 虛擬桌面用戶端](https://aka.ms/wvd/clients/windows)以開始使用。

如需 Windows 虛擬桌面上 Windows 7 的已知問題和疑難排解指示清單，請參閱疑難排解[Windows 虛擬桌面中的 windows 7 虛擬機器](troubleshoot-windows-7-vm.md)疑難排解一文。
