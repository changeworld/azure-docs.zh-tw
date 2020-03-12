---
title: Windows 10 企業版多會話常見問題-Azure
description: 針對 Windows 虛擬桌面使用 Windows 10 企業版多會話的常見問題和最佳作法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4301aaed0152e07eb2a941b56637239b66c33878
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127372"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 企業版的多重工作階段常見問題集

本文提供常見問題的解答，並說明 Windows 10 企業版多會話的最佳作法。
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>什麼是 Windows 10 企業版的多重會話？

Windows 10 企業版多會話，先前稱為 Windows 10 Enterprise for Virtual desktop （EVD），是允許多個並行互動會話的新遠端桌面工作階段主機。 先前只有 Windows Server 可以執行此動作。 這項功能可讓使用者熟悉 Windows 10 的體驗，同時也能受益于多重會話的成本優勢，並使用現有的每一使用者 Windows 授權，而不是 RDS 用戶端存取使用權（Cal）。 如需有關授權和定價的詳細資訊，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>有多少使用者可以同時在 Windows 10 企業版多會話上擁有互動式會話？

同時可作用中的互動式會話數目取決於您系統的硬體資源（vCPU、記憶體、磁片和 vGPU）、使用者在登入會話時使用其應用程式的方式，以及系統工作負載的繁重程度。 我們建議您驗證系統的效能，以瞭解您可以在 Windows 10 企業版多會話上擁有多少使用者。 若要深入瞭解，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>為什麼我的應用程式會將 Windows 10 企業版的多重會話報告成伺服器作業系統？

Windows 10 企業版多會話是 Windows 10 企業版的虛擬版本。 其中一項差異是，此作業系統（OS）將[ProductType](/windows/win32/cimwin32prov/win32-operatingsystem)的值報告為3，其值與 Windows Server 相同。 此屬性可讓 OS 與現有的 RDSH 管理工具、RDSH 多會話感知應用程式，以及針對 RDSH 環境的低層級系統效能優化相容。 某些應用程式安裝程式可以根據偵測到的 ProductType 是否設為 [用戶端]，封鎖 Windows 10 多會話安裝。 如果您的應用程式不會安裝，請洽詢您的應用程式廠商以取得更新的版本。 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>我可以在內部部署執行 Windows 10 企業版多會話嗎？

Windows 10 企業版多會話無法在內部部署生產環境中執行，因為它已針對適用于 Azure 的 Windows 虛擬桌面服務進行優化。 基於生產目的，它是針對在 Azure 外部執行 Windows 10 企業版多會話的授權合約。 Windows 10 企業版多會話不會針對內部部署金鑰管理服務（KMS）啟動。
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>如何? 為我的組織自訂 Windows 10 企業版多會話映射？

您可以使用 Windows 10 Windows 10 企業版多會話啟動 Azure 中的虛擬機器（VM），並透過安裝 LOB 應用程式、sysprep/一般化，然後使用 Azure 入口網站建立映射來進行自訂。  
 
若要開始使用，請在 Azure 中建立具有 Windows 10 Windows 10 企業版多會話的 VM。 您可以直接下載 VHD，而不是在 Azure 中啟動 VM。 之後，您將能夠使用您下載的 VHD，在已啟用 Hyper-v 的 Windows 10 電腦上建立新的第1代 VM。

藉由安裝 LOB 應用程式並 sysprep 映射，自訂映射以滿足您的需求。 當您完成自訂時，請將映射上傳至 Azure，並將其放入其中的 VHD。 之後，請從 Azure Marketplace 取得 Windows 虛擬桌面，並使用它來部署具有自訂映射的新主機集區。
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>如何? 在部署之後管理 Windows 10 企業版多會話嗎？

您可以使用任何支援的設定工具，但建議您使用1906版的 Configuration Manager，因為它支援 Windows 10 企業版的多重會話。 我們目前正在處理 Microsoft Intune 支援。
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Windows 10 企業版多會話可以 Azure Active Directory （AD）加入嗎？

目前支援將 Windows 10 企業版多會話加入混合式 Azure AD。 Windows 10 企業版多會話加入網域之後，請使用現有的群組原則物件來啟用 Azure AD 註冊。 如需詳細資訊，請參閱[規劃混合式 Azure Active Directory 聯結執行](../active-directory/devices/hybrid-azuread-join-plan.md)。
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>哪裡可以找到 Windows 10 企業版的多重會話映射？

Windows 10 企業版多會話位於 Azure 映射庫中。 若要尋找它，請流覽至 Azure 入口網站並搜尋 Windows 10 企業版的虛擬桌面版。 如需與 Office Pro Plus 整合的影像，請移至 Azure 入口網站並搜尋 Microsoft Windows 10 + Office 365 ProPlus。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>我應該使用哪一個 Windows 10 企業版的多重會話映射？

Azure 映射庫有數個版本，包括 Windows 10 企業版多會話、版本1809和 Windows 10 企業版的多重會話版本1903。 我們建議使用最新版本來改善效能和可靠性。
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>支援的 Windows 10 企業版多會話版本有哪些？

Windows 10 企業版的多重會話（1809和更新版本）受到支援，並可在 Azure 映射庫中取得。 這些版本遵循與 Windows 10 企業版相同的支援生命週期原則，這表示春季版本支援18個月，而秋季版本則為30個月。
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Windows 10 企業版多會話應該使用哪個設定檔管理解決方案？

當您在非持續環境中設定 Windows 10 企業版，或需要集中儲存設定檔的其他案例時，建議您使用 FSLogix 設定檔容器。 FSLogix 可確保每個使用者會話的使用者設定檔都可以使用並保持最新狀態。 我們也建議您使用 FSLogix 設定檔容器，將使用者設定檔儲存在任何具有適當許可權的 SMB 共用中，但如有必要，您可以將使用者設定檔儲存在 Azure 分頁 blob 儲存體中。 Windows 虛擬桌面使用者可以使用 FSLogix，而不需額外付費。
 
如需有關如何設定 FSLogix 設定檔容器的詳細資訊，請參閱[設定 FSLogix 設定檔容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>我需要哪一種授權才能存取 Windows 10 企業版的多重會話？

如需適用授權的完整清單，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>為什麼我的應用程式在我登出之後消失？

之所以會發生這種情況，是因為您使用 Windows 10 企業版多會話，並搭配像是 FSLogix 的設定檔管理解決方案。 您的系統管理員或設定檔解決方案已設定您的系統，在使用者登出時刪除使用者設定檔。此設定表示當您的系統在登出之後刪除使用者設定檔時，也會移除您在會話期間所安裝的任何應用程式。 如果您想要保留已安裝的應用程式，您必須要求系統管理員為您 Windows 虛擬桌面環境中的所有使用者布建這些應用程式。

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>如何? 在使用者登出時，確保應用程式不會消失嗎？

預設會設定大部分的虛擬化環境，以防止使用者將其他應用程式安裝到其設定檔。 如果您想要在使用者登出 Windows 虛擬桌面時確保應用程式不會消失，您必須為環境中的所有使用者設定檔布建該應用程式。 如需布建應用程式的詳細資訊，請參閱下列資源：

- [在 Windows 虛擬桌面中發佈內建應用程式](publish-apps.md)
- [DISM 應用程式套件服務命令列選項](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [新增-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>如何? 確保使用者不會從 Microsoft Store 下載並安裝應用程式嗎？

您可以停用 Microsoft Store 應用程式，以確保使用者不會下載額外的應用程式超過您已為其布建的應用程式。

若要停用儲存應用程式：

1. 建立新的群組原則。
2. 選取 **電腦**設定 > **系統管理範本** > **Windows 元件**。
3. 選取 [市集]。
4. 選取 [**儲存應用程式**]。
5. 選取 [**停用**]，然後選取 **[確定]** 。
6. 選取 [套用]。
 
## <a name="next-steps"></a>後續步驟

若要深入瞭解 Windows 虛擬桌面和 Windows 10 企業版多會話：

- 閱讀我們的[Windows 虛擬桌面預覽檔](overview.md)
- 造訪我們的[Windows 虛擬桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- 使用[Windows 虛擬桌面教學](tenant-setup-azure-active-directory.md)課程來設定您的 Windows 虛擬桌面部署
