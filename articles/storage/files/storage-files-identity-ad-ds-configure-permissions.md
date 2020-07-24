---
title: 控制使用者可以在檔案層級執行的動作-Azure 檔案共用
description: 瞭解如何為 Azure 檔案共用設定內部部署 AD DS 驗證的 Windows Acl 許可權。 讓您能夠利用細微的存取控制。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 5e293bb98405affd824d4bbc50b6f24c5a0e3c11
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999610"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>第三部分：透過 SMB 設定目錄和檔案層級許可權 

在開始本文之前，請先確定您已完成上一篇文章，[將共用層級許可權指派](storage-files-identity-ad-ds-assign-permissions.md)給身分識別，以確保您的共用層級許可權已準備就緒。

使用 RBAC 指派共用層級許可權之後，您必須在根、目錄或檔案層級設定適當的 Windows Acl，以利用細微的存取控制。 請將 RBAC 共用層級的許可權視為用來決定使用者是否可以存取共用的高階閘道管理員。 雖然 Windows Acl 會以更細微的層級運作，以判斷使用者可以在目錄或檔案層級執行哪些作業。 當使用者嘗試存取檔案/目錄時，會強制執行共用層級和檔案/目錄層級的許可權，因此如果兩者之間有差異，則只會套用最嚴格的一個。 例如，如果使用者在檔案層級具有讀取/寫入存取權，但唯讀取共用層級，則只能讀取該檔案。 如果是相反的，而且使用者具有共用層級的讀取/寫入存取權，但唯讀取檔案層級，他們仍然只能讀取檔案，同樣的情況也是如此。

## <a name="supported-permissions"></a>支援的許可權

Azure 檔案儲存體支援一組完整的基本和先進的 Windows Acl。 您可以在 Azure 檔案共用中，透過掛接共用，然後使用 Windows 檔案瀏覽器，執行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)命令或[Set ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl)命令，來查看和設定目錄和檔案上的 Windows acl。 

若要以超級使用者權限設定 Acl，您必須從已加入網域的 VM 使用您的儲存體帳戶金鑰來掛接共用。 遵循下一節中的指示，從命令提示字元掛接 Azure 檔案共用，並設定 Windows Acl。

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
|BUILTIN\Administrators|身為內部部署 AD DS 環境之網域系統管理員的所有使用者。
|BUILTIN\Users|AD 中的內建安全性群組。 根據預設，它包含 NT Authority\authenticated users 使用者。 若為傳統檔案伺服器，您可以設定每部伺服器的成員資格定義。 若為 Azure 檔案儲存體，則不會有主控伺服器，因此 BUILTIN\Users 包含與 NT Authority\authenticated users 使用者相同的一組使用者。|
|NT AUTHORITY\SYSTEM|檔案伺服器作業系統的服務帳戶。 這類服務帳戶不適用於 Azure 檔案儲存體內容。 它包含在根目錄中，以便與混合式案例的 Windows 檔案伺服器體驗一致。|
|NT Authority\authenticated users 使用者|AD 中可取得有效 Kerberos 權杖的所有使用者。|
|CREATOR OWNER|每個物件都有該物件的擁有者。 如果該物件上有指派給「CREATOR 擁有者」的 Acl，則屬於這個物件之擁有者的使用者會擁有 ACL 所定義之物件的許可權。|



## <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows `net use` 命令來掛接 Azure 檔案共用。 請記得使用您自己的值來取代下列範例中的預留位置值。 如需裝載檔案共用的詳細資訊，請參閱搭配[Windows 使用 Azure 檔案共用](storage-how-to-use-files-windows.md)。 

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

如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱[我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發行的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我們也會提供[指引](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)，以解決埠445遭到封鎖的案例。 

## <a name="configure-windows-acls"></a>設定 Windows Acl

使用儲存體帳戶金鑰掛接檔案共用之後，您必須設定 Windows Acl （也稱為 NTFS 許可權）。 您可以使用 Windows File Explorer 或 icacls 來設定 Windows Acl。

如果您在內部部署檔案伺服器中的目錄或檔案已針對 AD DS 身分識別設定 Windows Dacl，您可以將它複製到 Azure 檔案儲存體使用傳統的檔案複製工具（例如 Robocopy 或[Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases)）保存 acl。 如果您的目錄和檔案分層成透過 Azure 檔案同步 Azure 檔案儲存體，您的 Acl 會以其原生格式執行並保存。

### <a name="configure-windows-acls-with-windows-file-explorer"></a>使用 Windows File Explorer 設定 Windows Acl

使用 Windows 檔案瀏覽器，將完整許可權授與檔案共用下的所有目錄和檔案，包括根目錄。

1. 開啟 Windows 檔案瀏覽器，並以滑鼠右鍵按一下檔案/目錄，然後選取 [**屬性**]。
1. 選取 [安全性]**** 索引標籤。
1. 選取 [**編輯]。** 變更許可權。
1. 您可以變更現有使用者的許可權，或選取 [**新增**] 以將許可權授與新的使用者。
1. 在新增使用者的 [提示] 視窗中，于 [**輸入要選取的物件名稱**] 方塊中輸入您要授與許可權的目標使用者名稱，然後選取 [**檢查名稱**] 以尋找目標使用者的完整 UPN 名稱。
1.    選取 [確定]。
1.    在 [**安全性**] 索引標籤中，選取您想要授與新使用者的擁有權限。
1.    選取 [套用]。

### <a name="configure-windows-acls-with-icacls"></a>使用 icacls 設定 Windows Acl

使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

如需有關如何使用 icacls 來設定 Windows Acl 和不同類型的支援許可權的詳細資訊，請參閱[icacls 的命令列參考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="next-steps"></a>接下來的步驟

現在已啟用並設定此功能，接下來請繼續進行下一篇文章，您可以從已加入網域的 VM 掛接 Azure 檔案共用。

[第四部分：從已加入網域的 VM 掛接檔案共用](storage-files-identity-ad-ds-mount-file-share.md)
