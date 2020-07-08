---
title: 針對服務連線 Windows 虛擬桌面進行疑難排解 - Azure
description: 如何解決在 Windows 虛擬桌面租用戶環境中設定用戶端連線時的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 356506224a0273eeea65f0f901fbc79c338498d2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743609"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虛擬桌面服務連線

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。 如果您嘗試管理 2020 年春季版更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../troubleshoot-service-connection.md)。

使用此文章來解決 Windows 虛擬桌面用戶端連線的問題。

## <a name="provide-feedback"></a>提供意見反應

您可以在 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) \(英文\) 提供意見反應，並與產品小組和其他活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連線，但未顯示任何內容 (沒有摘要)

使用者可以啟動遠端桌面用戶端，而且能夠進行驗證，不過使用者在 Web 探索摘要中看不到任何圖示。

使用下列命令列，確認已將回報問題的使用者指派給應用程式群組：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

確認使用者是以正確的認證進行登入。

如果正在使用 Web 用戶端，請確認沒有任何快取的認證問題。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview-2019.md)。
- 若要針對在 Windows 虛擬桌面環境中建立租用戶與主機集區時的問題進行疑難排解，請參閱[租用戶和主機集區建立](troubleshoot-set-up-issues-2019.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration-2019.md)。
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell-2019.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
