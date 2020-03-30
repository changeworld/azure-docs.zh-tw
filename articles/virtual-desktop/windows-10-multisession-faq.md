---
title: Windows 10 企業多會話常見問題 - Azure
description: 為 Windows 虛擬桌面使用 Windows 10 企業多會話的常見問題和最佳實踐。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4301aaed0152e07eb2a941b56637239b66c33878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127372"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 企業版的多重工作階段常見問題集

本文回答了常見問題，並解釋了 Windows 10 企業多會話的最佳做法。
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>什麼是 Windows 10 企業多會話？

Windows 10 企業多會話（以前稱為適用于虛擬桌面的 Windows 10 企業版 ）是一個新的遠端桌面工作階段主機，允許多個併發互動式會話。 以前，只有 Windows 伺服器可以執行此操作。 此功能為使用者提供了熟悉的 Windows 10 體驗，而 IT 可以從多會話的成本優勢中獲益，並使用現有的每個使用者 Windows 許可，而不是 RDS 用戶端存取許可證 （CAL）。 有關許可證和定價的詳細資訊，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>有多少使用者可以同時在 Windows 10 企業多會話上同時具有互動式會話？

可以同時處於活動狀態的互動式會話有多少依賴于系統的硬體資源（vCPU、記憶體、磁片和 vGPU）、使用者在登錄到會話時如何使用其應用以及系統工作負載的繁重程度。 我們建議您驗證系統的性能，以瞭解在 Windows 10 企業多會話中可以擁有多少使用者。 要瞭解更多資訊，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>為什麼我的應用程式將 Windows 10 企業多會話報告為伺服器作業系統？

Windows 10 企業多會話是 Windows 10 企業版的虛擬版本。 其中一個區別是，此作業系統 （OS） 報告[ProductType](/windows/win32/cimwin32prov/win32-operatingsystem)的值為 3，與 Windows 伺服器的值相同。 此屬性使作業系統與現有的 RDSH 管理工具、RDSH 多會話感知應用程式以及 RDSH 環境的低級系統性能優化相容。 某些應用程式安裝程式可以阻止 Windows 10 多會話上的安裝，具體取決於它們是否檢測到 ProductType 設置為用戶端。 如果應用無法安裝，請與應用程式供應商聯繫以獲取更新的版本。 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>是否可以在本地運行 Windows 10 企業多會話？

Windows 10 企業多會話無法在本地生產環境中運行，因為它針對 Azure 的 Windows 虛擬桌面服務進行了優化。 出於生產目的在 Azure 外部運行 Windows 10 企業多會話是違反授權合約的。 Windows 10 企業多會話不會針對本地金鑰管理服務 （KMS） 啟動。
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>如何為我的組織自訂 Windows 10 企業多會話映射？

您可以使用 Windows 10 Windows 10 企業多會話在 Azure 中啟動虛擬機器 （VM），並通過安裝 LOB 應用程式、sysprep/通用化，然後使用 Azure 門戶創建映射來自訂它。  
 
要開始，請使用 Windows 10 Windows 10 企業多會話在 Azure 中創建 VM。 可以直接下載 VHD，而不是在 Azure 中啟動 VM。 之後，您將能夠使用下載的 VHD 在啟用了 Hyper-V 的 Windows 10 PC 上創建新的第 1 代 VM。

通過安裝 LOB 應用程式和系統準備映射，根據您的需要自訂映射。 完成自訂後，將映射上載到 Azure，其中包含 VHD。 之後，從 Azure 應用商店獲取 Windows 虛擬桌面，並用它來部署具有自訂映射的新主機池。
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>部署後如何管理 Windows 10 企業多會話？

您可以使用任何受支援的組態工具，但我們建議組態管理員 版本 1906，因為它支援 Windows 10 企業多會話。 我們目前正在開發微軟 Intune 支援。
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 企業多會話是否可以加入 Azure 活動目錄 （AD）？

Windows 10 企業多會話當前受支援為混合 Azure AD 加入。 在 Windows 10 企業多會話加入域後，使用現有的群組原則物件啟用 Azure AD 註冊。 有關詳細資訊，請參閱[規劃混合 Azure 活動目錄聯接實現](../active-directory/devices/hybrid-azuread-join-plan.md)。
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>在哪裡可以找到 Windows 10 企業多會話映射？

Windows 10 企業多會話位於 Azure 庫中。 要找到它，導航到 Azure 門戶並搜索適用于虛擬桌面的 Windows 10 企業版。 有關與 Office Pro Plus 集成的圖像，請訪問 Azure 門戶並搜索 Microsoft Windows 10 + Office 365 專業增強版。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>我應該使用哪種 Windows 10 企業多會話映射？

Azure 庫有多個版本，包括 Windows 10 企業多會話、版本 1809 和 Windows 10 企業多會話版本 1903。 我們建議使用最新版本來提高性能和可靠性。
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>支援哪些 Windows 10 企業多會話版本？

Windows 10 企業多會話版本 1809 及更高版本受支援，可在 Azure 庫中使用。 這些版本遵循與 Windows 10 企業版相同的支援生命週期策略，這意味著春季版本支援 18 個月，秋季版本支援 30 個月。
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>我應該使用哪種設定檔管理解決方案用於 Windows 10 企業多會話？

我們建議您在非持久性環境或其他需要集中存儲設定檔的方案中配置 Windows 10 企業版時使用 FSLogix 設定檔容器。 FSLogix 確保每個使用者會話的使用者設定檔可用和最新。 我們還建議使用 FSLogix 設定檔容器將使用者設定檔存儲在具有適當許可權的任何 SMB 共用中，但如有必要，可以在 Azure 頁 Blob 存儲中存儲使用者設定檔。 Windows 虛擬桌面使用者可以免費使用 FSLogix。
 
有關如何配置 FSLogix 設定檔容器的詳細資訊，請參閱[配置 FSLogix 設定檔容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>訪問 Windows 10 企業多會話需要哪個許可證？

有關適用許可證的完整清單，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>為什麼我的應用在登出後會消失？

這是因為您使用 Windows 10 企業多會話，具有設定檔管理解決方案（如 FSLogix）。 您的管理員或設定檔解決方案配置了您的系統，以便使用者登出時刪除使用者設定檔。此配置意味著，當您的系統在登出後刪除使用者設定檔時，系統還會刪除您在會話期間安裝的任何應用。 如果要保留已安裝的應用，則需要要求管理員為 Windows 虛擬桌面環境中的所有使用者預配這些應用。

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>如何確保應用在使用者登出時不會消失？

預設情況下，大多數虛擬化環境都進行了配置，以防止使用者將其他應用安裝到其設定檔中。 如果要確保應用在使用者退出 Windows 虛擬桌面時不會消失，則必須為環境中的所有使用者設定檔預配該應用。 有關預配應用的詳細資訊，請查看以下資源：

- [在 Windows 虛擬桌面中發佈內置應用](publish-apps.md)
- [DISM 應用包服務命令列選項](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [附加應用預配包](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>如何確保使用者不會從 Microsoft 應用商店下載和安裝應用？

您可以禁用 Microsoft 應用商店應用，以確保使用者不會下載超出已為其預配的應用之外的額外應用。

要禁用應用商店應用，

1. 創建新的群組原則。
2. 選擇**電腦配置** > **管理範本** > **視窗元件**。
3. 選取 [市集] ****。
4. 選擇**存儲應用程式**。
5. 選擇 **"已禁用**"，然後選擇 **"確定**"。
6. 選取 [**套用**]。
 
## <a name="next-steps"></a>後續步驟

要瞭解有關 Windows 虛擬桌面和 Windows 10 企業多會話的更多資訊，：

- 閱讀我們的[Windows 虛擬桌面預覽文檔](overview.md)
- 訪問我們的[Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- 使用[Windows 虛擬桌面教程](tenant-setup-azure-active-directory.md)設置 Windows 虛擬桌面部署
