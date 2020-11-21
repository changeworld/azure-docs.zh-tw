---
title: Windows 10 企業版多會話常見問題-Azure
description: 針對 Windows 虛擬桌面使用 Windows 10 企業版多重會話的常見問題和最佳作法。
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24d27859aad7642392a5702ab7ddbb727b538d51
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025005"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 企業版的多重工作階段常見問題集

本文提供常見問題的解答，並說明 Windows 10 企業版多會話的最佳作法。

## <a name="what-is-windows-10-enterprise-multi-session"></a>什麼是 Windows 10 企業版多重會話？

Windows 10 企業版多會話（前身為虛擬桌面 (EVD) 的 Windows 10 企業版）是新的遠端桌面工作階段主機，可允許多個並行的互動式會話。 先前，只有 Windows Server 可以這樣做。 這項功能可提供使用者熟悉的 Windows 10 體驗，並可從多個會話的成本優勢獲益，並使用現有的每一使用者 Windows 授權，而不是 (Cal) 的 RDS 用戶端存取授權。 如需有關授權和定價的詳細資訊，請參閱 [Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>有多少使用者可以在 Windows 10 企業版多個會話上同時有互動式會話？

同時可以啟用的互動式會話數量，會依賴您系統的硬體資源 (vCPU、記憶體、磁片和 vGPU) 、使用者在登入會話時如何使用其應用程式，以及系統工作負載的繁重程度。 我們建議您驗證您的系統效能，以瞭解您可以 Windows 10 企業版多個會話的使用者數目。 若要深入瞭解，請參閱 [Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>為什麼我的應用程式會以伺服器作業系統的形式 Windows 10 企業版多重會話報告？

Windows 10 企業版多會話是 Windows 10 企業版的虛擬版本。 其中一項差異在於，此作業系統 (OS) 將 [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) 回報為值3，與 Windows Server 的值相同。 這個屬性會將作業系統與現有的 RDSH 管理工具、RDSH 多會話感知的應用程式，以及大部分的 RDSH 環境的低層級系統效能優化保持相容。 某些應用程式安裝程式可以根據偵測到 ProductType 是否設定為用戶端，封鎖 Windows 10 多個會話的安裝。 如果您的應用程式不會安裝，請洽詢您的應用程式廠商以取得更新的版本。

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>我可以在內部部署環境中執行 Windows 10 企業版多個會話嗎？

Windows 10 企業版多會話無法在內部部署生產環境中執行，因為它已針對適用于 Azure 的 Windows 虛擬桌面服務進行優化。 這是基於生產目的，在 Azure 外部執行 Windows 10 企業版多會話的授權合約。 Windows 10 企業版多會話不會針對內部部署金鑰管理服務啟用 (KMS) 。

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>如何? 針對我的組織自訂 Windows 10 企業版多會話映射？

您可以使用 Windows 10 Windows 10 企業版多重會話來啟動 Azure 中 (VM) 的虛擬機器，並藉由安裝 LOB 應用程式、sysprep/一般化來自訂，然後使用 Azure 入口網站建立映射。

若要開始使用，請在 Azure 中建立具有 Windows 10 企業版多會話的 VM。 您可以直接下載 VHD，而不是在 Azure 中啟動 VM。 之後，您將能夠使用您所下載的 VHD，在啟用 Hyper-v 的 Windows 10 電腦上建立新的第1代 VM。

藉由安裝 LOB 應用程式和 sysprep 來自訂映射，以滿足您的需求。 當您完成自訂時，請將映射上傳至 Azure，並在內部使用 VHD。 之後，從 Azure Marketplace 取得 Windows 虛擬桌面，並使用它來部署具有自訂映射的新主機集區。

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>如何? 在部署後管理 Windows 10 企業版多會話？

您可以使用任何支援的設定工具，但我們建議 Configuration Manager 版本1906，因為它支援 Windows 10 企業版多會話。 我們目前正致力於 Microsoft Intune 支援。

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>是否可以 Azure Active Directory (AD) 聯結來 Windows 10 企業版多會話？

Windows 10 企業版多個會話目前支援混合式 Azure AD 聯結。 Windows 10 企業版多會話加入網域之後，請使用現有的群組原則物件來啟用 Azure AD 註冊。 如需詳細資訊，請參閱 [規劃混合式 Azure Active Directory 聯結執行](../active-directory/devices/hybrid-azuread-join-plan.md)。

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>我可以在哪裡找到 Windows 10 企業版多會話映射？

Windows 10 企業版多個會話位於 Azure 資源庫中。 若要找到它，請流覽至 Azure 入口網站並搜尋虛擬桌面版本的 Windows 10 企業版。 若為與企業 Microsoft 365 應用程式整合的映射，請移至 Azure 入口網站，並搜尋適用 **于 enterprise 的 Microsoft Windows 10 + Microsoft 365 應用程式**。

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>我應該使用哪一個 Windows 10 企業版多會話映射？

Azure 資源庫有數個版本，包括 Windows 10 企業版多會話、1809版，以及 Windows 10 企業版多會話1903版。 我們建議使用最新版本，以改善效能和可靠性。

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>支援哪些 Windows 10 企業版多會話版本？

支援 Windows 10 企業版多會話版本1809和更新版本，並可在 Azure 資源庫中取得。 這些版本遵循與 Windows 10 企業版相同的支援生命週期原則，這表示年3月版本支援18個月和9月發行30個月。

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>我應該使用哪個設定檔管理解決方案來 Windows 10 企業版多個會話？

當您在非持續性環境或需要集中儲存設定檔的其他案例中設定 Windows 10 企業版時，建議使用 FSLogix 設定檔容器。 FSLogix 可確保使用者設定檔可供使用，且每個使用者會話都是最新的。 我們也建議您使用 FSLogix 設定檔容器，將使用者設定檔儲存在具有適當許可權的任何 SMB 共用中，但如有必要，您可以將使用者設定檔儲存在 Azure 分頁 blob 儲存體中。 Windows 虛擬桌面使用者不需額外付費，即可使用 FSLogix。

如需有關如何設定 FSLogix 設定檔容器的詳細資訊，請參閱 [設定 FSLogix 設定檔容器](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>我需要哪些授權才能存取 Windows 10 企業版多會話？

如需適用授權的完整清單，請參閱 [Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>為什麼我的應用程式在我登出之後會消失？

發生這種情況是因為您使用 Windows 10 企業版多會話與設定檔管理解決方案（例如 FSLogix）。 您的系統管理員或設定檔解決方案已設定您的系統在使用者登出時刪除使用者設定檔。這種設定表示當您的系統在登出之後刪除您的使用者設定檔時，也會移除您在會話期間安裝的任何應用程式。 如果您想要保留您安裝的應用程式，您必須要求系統管理員為您的 Windows 虛擬桌面環境中的所有使用者布建這些應用程式。

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>如何? 確定使用者登出時應用程式不會消失？

預設會設定大部分的虛擬化環境，以防止使用者將其他應用程式安裝至其設定檔。 如果您想要在使用者登出 Windows 虛擬桌面時，確保應用程式不會消失，您必須為環境中的所有使用者設定檔布建該應用程式。 如需有關布建應用程式的詳細資訊，請參閱下列資源：

- [在 Windows 虛擬桌面中發佈內建應用程式](publish-apps.md)
- [DISM 應用程式套件服務命令列選項](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [新增-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>如何? 確定使用者不會從 Microsoft Store 下載及安裝應用程式？

您可以停用 Microsoft Store 應用程式，以確保使用者不會將額外的應用程式下載到您已為其布建的應用程式以外的應用程式。

若要停用 Store 應用程式：

1. 建立新的群組原則。
2. 選取 [**電腦** 設定  >  **系統管理範本**  >  **Windows 元件**]。
3. 選取 [市集] 。
4. 選取 [ **存放區應用程式**]。
5. 選取 [ **已停用**]，然後選取 **[確定]**。
6. 選取 [套用]。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Windows 虛擬桌面和 Windows 10 企業版多個會話：

- 閱讀我們的 [Windows 虛擬桌面檔](overview.md)
- 造訪我們的 [Windows 虛擬桌面 >techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- 使用[Windows 虛擬桌面教學](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)課程設定您的 Windows 虛擬桌面部署