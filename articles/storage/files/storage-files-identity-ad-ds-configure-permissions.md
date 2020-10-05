---
title: 控制使用者可以在檔案層級進行的動作-Azure 檔案共用
description: 瞭解如何為內部部署 AD DS Azure 檔案共用的驗證設定 Windows Acl 許可權。 允許您利用細微的存取控制。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 50753950556531ed3915292f44668073b88be45b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716027"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>第三部分：透過 SMB 設定目錄和檔案層級許可權 

開始本文之前，請確定您已完成前一篇文章， [將共用層級許可權指派給身分識別](storage-files-identity-ad-ds-assign-permissions.md) ，以確保您的共用層級許可權已就緒。

使用 Azure RBAC 指派共用層級許可權之後，您必須在根、目錄或檔案層級設定適當的 Windows Acl，以利用細微的存取控制。 請將 Azure RBAC 共用層級的許可權視為可決定使用者是否可以存取共用的高階閘道管理員。 雖然 Windows Acl 會以更細微的層級運作，以判斷使用者可在目錄或檔案層級執行哪些作業。 當使用者嘗試存取檔案/目錄時，會強制執行共用層級和檔案/目錄層級許可權，因此如果兩者之間有差異，則只會套用限制最嚴格的許可權。 例如，如果使用者在檔案層級擁有讀取/寫入存取權，但只在共用層級讀取，則只能讀取該檔案。 如果已反轉，且使用者在共用層級擁有讀取/寫入存取權，但只在檔案層級讀取，則相同也會是 true，但只能讀取檔案。

## <a name="azure-rbac-permissions"></a>Azure RBAC 許可權

下表包含與此設定相關的 Azure RBAC 許可權：


| 內建角色  | NTFS 許可權  | 產生的存取權  |
|---------|---------|---------|
|儲存體檔案資料 SMB 共用讀者 | 完全控制、修改、讀取、寫入、執行 | 讀取和執行  |
|     |   讀取 |     讀取  |
|儲存體檔案資料 SMB 共用參與者  |  完整控制    |  修改、讀取、寫入、執行 |
|     |  修改         |  修改    |
|     |  讀取和執行 |  讀取和執行 |
|     |  讀取           |  讀取    |
|     |  寫入          |  寫入   |
|儲存體檔案資料 SMB 共用提升權限的參與者 | 完整控制  |  修改、讀取、寫入、編輯、執行 |
|     |  修改          |  修改 |
|     |  讀取和執行  |  讀取和執行 |
|     |  讀取            |  讀取   |
|     |  寫入           |  寫入  |



## <a name="supported-permissions"></a>支援的許可權

Azure 檔案儲存體支援一組完整的基本和 advanced Windows Acl。 您可以透過掛接共用，然後使用 Windows 檔案總管、執行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 命令或 [Set ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) 命令，在 Azure 檔案共用中的目錄和檔案上查看和設定 Windows acl。 

若要使用超級使用者權限設定 Acl，您必須使用已加入網域的 VM 中的儲存體帳戶金鑰來掛接共用。 遵循下一節中的指示，從命令提示字元掛接 Azure 檔案共用，並設定 Windows Acl。

檔案共用的根目錄包含下列許可權：

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|使用者|定義|
|---|---|
|BUILTIN\Administrators|身為內部內部部署 AD DS 環境之網域系統管理員的所有使用者。
|BUILTIN\Users|AD 中的內建安全性群組。 依預設，它包含 NT Authority\authenticated users Users。 若為傳統檔案伺服器，您可以設定每部伺服器的成員資格定義。 針對 Azure 檔案儲存體，沒有主控伺服器，因此 BUILTIN\Users 包含與 NT Authority\authenticated users 使用者相同的一組使用者。|
|NT AUTHORITY\SYSTEM|檔案伺服器作業系統的服務帳戶。 這類服務帳戶不適用 Azure 檔案儲存體內容。 它包含在根目錄中，與混合式案例的 Windows 檔案伺服器體驗一致。|
|NT Authority\authenticated users 使用者|AD 中的所有使用者都可以取得有效的 Kerberos 權杖。|
|CREATOR OWNER|目錄或檔案中的每個物件都有該物件的擁有者。 如果有 Acl 指派給該物件的「建立者擁有者」，則為此物件之擁有者的使用者具有 ACL 所定義之物件的許可權。|



## <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows `net use` 命令來掛接 Azure 檔案共用。 請記得使用您自己的值來取代下列範例中的預留位置值。 如需掛接檔案共用的詳細資訊，請參閱搭配 [Windows 使用 Azure 檔案共用](storage-how-to-use-files-windows.md)。 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱 [我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發佈的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我們也會在埠445遭到封鎖時，提供解決案例的 [指引](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 。 

## <a name="configure-windows-acls"></a>設定 Windows Acl

使用儲存體帳戶金鑰掛接您的檔案共用之後，您必須設定 Windows Acl (也稱為 NTFS 許可權) 。 您可以使用 Windows 檔案總管或 icacls 來設定 Windows Acl。

如果您在內部部署檔案伺服器中有針對 AD DS 身分識別設定 Windows Dacl 的目錄或檔案，您可以將它複製到 Azure 檔案儲存體利用傳統的檔案複製工具（例如 Robocopy 或 [Azure AzCopy v1.0 +](https://github.com/Azure/azure-storage-azcopy/releases)）保存 acl。 如果您的目錄和檔案分層至透過 Azure 檔案同步 Azure 檔案儲存體，您的 Acl 會以原生格式保存並保存。

### <a name="configure-windows-acls-with-windows-file-explorer"></a>使用 Windows 檔案總管設定 Windows Acl

使用 Windows 檔案總管將完整許可權授與檔案共用下的所有目錄和檔案，包括根目錄在內。

1. 開啟 [Windows 檔案總管並以滑鼠右鍵按一下檔案/目錄，然後選取 [ **屬性**]。
1. 選取 [安全性]**** 索引標籤。
1. 選取 [ **編輯]。** 變更許可權。
1. 您可以變更現有使用者的許可權， **或選取 [新增]** ，將許可權授與新的使用者。
1. 在 [新增使用者的提示] 視窗中，在 [ **輸入物件名稱來選取** ] 方塊中輸入您想要授與許可權的目標使用者名稱，然後選取 [ **檢查名稱** ] 以尋找目標使用者的完整 UPN 名稱。
1.    選取 [確定]  。
1.    在 [ **安全性** ] 索引標籤中，選取您要授與新使用者的擁有權限。
1.    選取 [套用]。

### <a name="configure-windows-acls-with-icacls"></a>使用 icacls 設定 Windows Acl

使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

如需有關如何使用 icacls 來設定 Windows Acl 以及不同類型的支援許可權的詳細資訊，請參閱 [icacls 的命令列參考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="next-steps"></a>後續步驟

現在已啟用並設定此功能，請繼續進行下一篇文章，您可以從已加入網域的 VM 掛接 Azure 檔案共用。

[第四部分：從已加入網域的 VM 掛接檔案共用](storage-files-identity-ad-ds-mount-file-share.md)
