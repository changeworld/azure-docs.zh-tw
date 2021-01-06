---
title: Windows 虛擬桌面設定檔案共用 MSIX 應用程式附加預覽-Azure
description: 如何設定適用于 Windows 虛擬桌面的 MSIX 應用程式附加檔案共用。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930781"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>設定 MSIX 應用程式附加的檔案共用 (預覽) 

> [!IMPORTANT]
> MSIX 應用程式附加目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

所有 MSIX 映射都必須儲存在網路共用上，並可供具有唯讀許可權的主機集區中的使用者存取。

MSIX 應用程式附加 (預覽版) 與檔案共用所使用的儲存網狀架構類型沒有任何相依性。 MSIX app 附加共用的考慮與 FSLogix 共用的考慮相同。 若要深入瞭解儲存體需求，請參閱 [Windows 虛擬桌面中 FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。

## <a name="performance-requirements"></a>效能需求

MSIX 應用程式的應用程式連接映射大小限制取決於您用來儲存 VHD 或 VHDx 檔案的儲存體類型，以及 VHD、VHSD 或 CIM 檔案和檔案系統的大小限制。

下表提供一個範例，其中包含每個 VM 的單一 1 GB MSIX 映射，其中有一個 MSIX 應用程式需要每個 VM：

| 資源             | 規格需求 |
|----------------------|--------------|
| 穩定狀態 IOPs    | 1 IOPs       |
| 電腦開機登入 | 10 IOPs      |
| 延遲              | 400毫秒       |

需求可能會有很大的差異，視 MSIX 映射中儲存的 MSIX 封裝應用程式數目而定。 針對較大的 MSIX 映射，您必須配置更多頻寬。

### <a name="storage-recommendations"></a>儲存體建議

Azure 提供多個可用於 MISX 應用程式附加的儲存體選項。 建議您使用 Azure 檔案儲存體或 Azure NetApp Files，因為這些選項會提供成本與管理額外負荷之間的最佳價值。 在 [Windows 虛擬桌面中 FSLogix 設定檔容器](store-fslogix-profile.md) 的發行項儲存選項，會比較 Azure 在 Windows 虛擬桌面內容中所提供的不同受控儲存體解決方案。

### <a name="optimize-msix-app-attach-performance"></a>優化 MSIX 應用程式附加效能

以下是我們建議您將 MSIX 應用程式附加效能優化的其他事項：

- 您用於 MSIX 應用程式附加的儲存體解決方案應該與會話主機位於相同的資料中心位置。
- 若要避免效能瓶頸，請從防毒軟體掃描中排除下列 VHD、VHDX 和 CIM 檔案：
   
    - <MSIXAppAttachFileShare \> \* 。VHD
    - <MSIXAppAttachFileShare \> \* 。VHDX
    - \\\\storageaccount.file.core.windows.net \\ 共用 \* \* 。VHD
    - \\\\storageaccount.file.core.windows.net \\ 共用 \* \* 。VHDX
    - <MSIXAppAttachFileShare>.Cim
    - \\\\storageaccount.file.core.windows.net \\ 共用 \* \* 。Cim

- 將 MSIX 應用程式附加的存放網狀架構從 FSLogix 設定檔容器區隔開。
- 所有 VM 系統帳戶和使用者帳戶都必須擁有唯讀許可權，才能存取檔案共用。
- 任何適用于 Windows 虛擬桌面的嚴重損壞修復計畫都必須包含在次要容錯移轉位置複寫 MSIX 應用程式附加檔案共用。 若要深入瞭解損毀修復，請參閱 [設定商務持續性和嚴重損壞修復計畫](disaster-recovery.md)。

## <a name="how-to-set-up-the-file-share"></a>如何設定檔案共用

MSIX 應用程式附加檔案共用的安裝程式與 [FSLogix 設定檔共用的安裝](create-host-pools-user-profile.md)程式大致相同。 不過，您必須為使用者指派不同的許可權。 MSIX 應用程式附加需要唯讀許可權才能存取檔案共用。

如果您要將 MSIX 應用程式儲存在 Azure 檔案儲存體中，則針對您的工作階段主機，您必須將儲存體帳戶角色型存取控制指派給所有工作階段主機 Vm (RBAC) 和檔案共用新技術檔案系統 (NTFS) 共用的許可權。

| Azure 物件                      | 必要角色                                     | Role 函數                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| 工作階段主機 (VM 電腦物件) | 儲存體檔案資料 SMB 共用參與者          | 讀取和執行、讀取、列出資料夾內容  |
| 檔案共用上的系統管理員              | 儲存體檔案資料 SMB 共用提升權限的參與者 | 完整控制                                  |
| 檔案共用上的使用者               | 儲存體檔案資料 SMB 共用參與者          | 讀取和執行、讀取、列出資料夾內容  |

指派儲存體帳戶和檔案共用的工作階段主機 Vm 許可權：

1. 建立 Active Directory Domain Services (AD DS) 安全性群組。

2. 將所有工作階段主機 Vm 的電腦帳戶新增為群組的成員。

3. 將 AD DS 群組同步至 Azure Active Directory (Azure AD) 。

4. 建立儲存體帳戶。

5. 遵循 [建立 Azure 檔案共用](../storage/files/storage-how-to-create-file-share.md#create-file-share)中的指示，在儲存體帳戶底下建立檔案共用。

6. 遵循第一節中的指示，將儲存體帳戶加入 AD DS [：為 Azure 檔案共用啟用 AD DS 驗證](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)。

7. 將同步處理的 AD DS 群組指派給 Azure AD，並為儲存體帳戶指派「儲存體檔案資料 SMB 共用參與者」角色。

8. 遵循第二部分中的指示，將檔案共用掛接到任何工作階段主機 [：將共用層級許可權指派給身分識別](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)。

9. 將檔案共用的 NTFS 許可權授與 AD DS 群組。

10. 設定使用者帳戶的 NTFS 許可權。 您將需要一個作業單位 (OU) 來自 VM 中帳戶所屬的 AD DS。

將身分識別指派給您的儲存體之後，請遵循 [後續步驟](#next-steps) 中文章中的指示，將其他必要許可權授與給您已指派給 vm 的身分識別。

您也必須確定您的工作階段主機 Vm 具有新的技術檔案系統 (NTFS) 許可權。 您必須擁有來自 Active Directory Domain Services (AD DS) 的營運單位容器，而您的使用者必須是該操作單位的成員，才能使用這些許可權。

## <a name="next-steps"></a>後續步驟

以下是您在設定檔案共用之後必須執行的其他作業：

- 瞭解如何設定 Azure Active Directory Domain Services (AD DS) 建立 [具有 Azure 檔案儲存體和 AD DS 的設定檔容器](create-file-share.md)。
- 瞭解如何在 [建立具有 Azure 檔案儲存體和 AZURE AD DS 之設定檔容器](create-profile-container-adds.md)的 Azure 檔案儲存體和 Azure AD ds。
- 瞭解如何在 [使用 Azure NetApp files 和 AD DS 建立設定檔容器](create-fslogix-profile-container.md)時，設定適用于 MSIX app 附加的 Azure NetApp files。
- 瞭解如何在 [使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)時，使用虛擬機器型檔案共用。

完成之後，以下是您可能會覺得有用的一些其他資源：

- 在 [Windows 虛擬桌面 >techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)詢問有關這項功能的問題。
- 您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)留下 Windows 虛擬桌面的意見反應。
- [MSIX 應用程式附加詞彙](app-attach-glossary.md)
- [MSIX 應用程式附加常見問題](app-attach-faq.md)
