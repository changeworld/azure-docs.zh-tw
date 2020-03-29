---
title: 部署 Windows 7 虛擬機器 Windows 虛擬桌面 - Azure
description: 如何在 Windows 虛擬桌面上配置和部署 Windows 7 虛擬機器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366678"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>在 Windows 虛擬桌面上部署 Windows 7 虛擬機器

在 Windows 虛擬桌面上部署 Windows 7 虛擬機器 （VM） 的過程與運行更高版本的 Windows 的 VM 略有不同。 本指南將告訴您如何部署 Windows 7。

## <a name="prerequisites"></a>Prerequisites

開始之前，請按照使用[PowerShell 創建主機池](create-host-pools-powershell.md)的說明創建主機池以創建主機池。 之後，按照[Azure 應用商店中創建主機池中的](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group)說明將一個或多個使用者分配給桌面應用程式組。

## <a name="configure-a-windows-7-virtual-machine"></a>配置 Windows 7 虛擬機器

完成先決條件後，即可配置 Windows 7 VM 以在 Windows 虛擬桌面上部署。

要在 Windows 虛擬桌面上設置 Windows 7 VM，可以：

1. 登錄到 Azure 門戶並搜索 Windows 7 企業版映射或上傳自己的自訂 Windows 7 企業版 （x64） 映射。  
2. 部署一個或多個以 Windows 7 企業版為主機作業系統的虛擬機器。 確保虛擬機器允許遠端桌面協定 （RDP）（TCP/3389 埠）。
3. 使用 RDP 連接到 Windows 7 企業版主機，並使用配置部署時定義的憑據進行身份驗證。 
4. 將使用 RDP 連接到主機時使用的帳戶添加到"遠端桌面使用者"組。 如果不執行此操作，則在將其加入到 Active Directory 域後可能無法連接到 VM。
5. 轉到 VM 上的 Windows 更新。
6. 在"重要"類別中安裝所有 Windows 更新。
7. 在"可選"類別中安裝所有 Windows 更新（不包括語言包）。 這將安裝需要完成這些說明的遠端桌面協定 8.0 更新 （[KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)）。
8. 打開本機群組策略編輯器並導航到**電腦配置** > **管理範本** > **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **遠端會話環境**。
9. 啟用遠端桌面協定 8.0 策略。
10. 將此 VM 加入您的活動目錄域。
11. 通過運行以下命令重新開機虛擬機器：
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. 按照[此處](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/)的說明獲取註冊權杖。
13. [下載適用于 Windows 7 的 Windows 虛擬桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下載 Windows 7 的 Windows 虛擬桌面代理管理器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 打開 Windows 虛擬桌面代理安裝程式並按照說明操作。 出現提示後，請提供您在步驟 12 中創建的註冊金鑰。
16. 打開 Windows 虛擬桌面安裝程式並按照說明操作。
17. 或者，阻止 TCP/3389 埠以刪除對 VM 的直接遠端桌面協定訪問。

## <a name="next-steps"></a>後續步驟

Windows 虛擬桌面部署現已準備就緒，可供使用。 [下載最新版本的 Windows 虛擬桌面用戶端](https://aka.ms/wvd/clients/windows)以開始。

有關 Windows 虛擬桌面上的 Windows 7 的已知問題和故障排除說明的清單，請參閱我們在[Windows 虛擬桌面中的 Windows 7 虛擬機器疑難排解](troubleshoot-windows-7-vm.md)一文。
