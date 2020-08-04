---
title: 將 Azure 檔案共用掛接至已加入 AD DS 的 VM
description: 瞭解如何將檔案共用掛接到已加入內部部署 Active Directory Domain Services 的電腦。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9807563c768b82c823ff754aaa679ddc917bf62d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535054"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>第四部分：從已加入網域的 VM 掛接檔案共用

在開始本文之前，請確定您已完成上一篇文章：透過[SMB 設定目錄和檔案層級許可權](storage-files-identity-ad-ds-configure-permissions.md)。

本文中所述的程式會確認您的檔案共用和存取權限已正確設定，而且您可以從已加入網域的 VM 存取 Azure 檔案共用。 共用層級的 Azure 角色指派可能需要一些時間才會生效。 

使用您授與許可權的認證來登入用戶端，如下圖所示。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>裝載必要條件

在您可以掛接檔案共用之前，請確定您已完成下列必要條件：

- 如果您要從先前使用儲存體帳戶金鑰掛接檔案共用的用戶端掛接檔案共用，請確定您已中斷共用、移除儲存體帳戶金鑰的持續性認證，而且目前正在使用 AD DS 認證來進行驗證。
- 您的用戶端必須能夠看到您的 AD DS。 如果您的電腦或 VM 超出 AD DS 管理的網路，您將需要啟用 VPN 以連線 AD DS 以進行驗證。

將預留位置值取代為您自己的值，然後使用下列命令來掛接 Azure 檔案共用：

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果您遇到以 AD DS 認證掛接的問題，請參閱[無法使用 AD 認證來裝載 Azure 檔案儲存體](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)以取得指導方針。

如果掛接您的檔案共用成功，表示您已成功啟用並設定 Azure 檔案共用的內部部署 AD DS 驗證。

## <a name="next-steps"></a>後續步驟

如果您在 AD DS 中建立以代表儲存體帳戶的身分識別是在強制執行密碼輪替的網域或 OU 中，請繼續閱讀下一篇文章，以取得有關更新密碼的指示：

[在 AD DS 中更新儲存體帳戶身分識別的密碼](storage-files-identity-ad-ds-update-password.md)
