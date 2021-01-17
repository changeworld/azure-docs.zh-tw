---
title: 針對服務連線 Windows 虛擬桌面進行疑難排解 - Azure
description: 如何在 Windows 虛擬桌面租使用者環境中設定服務連接時解決問題。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539079"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows 虛擬桌面服務連線

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)。

使用此文章來解決 Windows 虛擬桌面用戶端連線的問題。

## <a name="provide-feedback"></a>提供意見反應

您可以在 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) \(英文\) 提供意見反應，並與產品小組和其他活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連線，但未顯示任何內容 (沒有摘要)

使用者可以啟動遠端桌面用戶端，而且能夠進行驗證，不過使用者在 Web 探索摘要中看不到任何圖示。

1. 使用下列命令列，確認已將回報問題的使用者指派給應用程式群組：

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. 確認使用者是以正確的認證進行登入。

3. 如果正在使用 Web 用戶端，請確認沒有任何快取的認證問題。

4. 如果使用者是 Azure Active Directory (AD) 使用者群組的一部分，請確定使用者群組是安全性群組，而不是通訊群組。 Windows 虛擬桌面不支援 Azure AD 通訊群組。

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>使用者遺失現有的摘要，而且沒有顯示任何遠端資源 (沒有摘要) 

當使用者將訂用帳戶從一個 Azure AD 租使用者移至另一個租使用者時，通常會出現此錯誤。 如此一來，服務就會失去其使用者指派的追蹤，因為它們仍系結至舊的 Azure AD 租使用者。

若要解決此問題，您只需將使用者重新指派給其應用程式群組。

如果 CSP 提供者已建立訂用帳戶，然後傳送給客戶，也可能會發生這種情況。 若要解決此問題，請重新註冊資源提供者。

1. 登入 Azure 入口網站。
2. 移至 [ **訂** 用帳戶]，然後選取您的訂用帳戶。
3. 在頁面左側的功能表中，選取 [ **資源提供者**]。
4. 尋找並選取 [ **DesktopVirtualization**]，然後選取 [ **重新註冊**]。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立 Windows 虛擬桌面環境和主機集區時的問題進行疑難排解，請參閱[環境和主機集區建立](troubleshoot-set-up-issues.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要針對與 Windows 虛擬桌面代理程式或會話連線相關的問題進行疑難排解，請參閱針對 [常見的 Windows 虛擬桌面代理程式問題進行疑難排解](troubleshoot-agent.md)。
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
