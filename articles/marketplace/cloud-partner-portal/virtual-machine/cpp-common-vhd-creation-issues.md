---
title: Azure 應用商店 VHD 創建 (FAQ) 期間的常見問題
description: 建立 VHD 的常見問題與相關問題。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: 988121effb35949cce442ba9458789105e88feec
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273438"
---
# <a name="common-issues-during-vhd-creation-faq"></a>VHD 建立過程中的常見問題 (常見問題集)

> [!IMPORTANT]
> 從 2020 年 4 月 13 日開始,我們將開始將 Azure 虛擬機產品的管理移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建 VHD 常見問題解答](https://aka.ms/VHDcreationIssues)中的說明進行操作,以管理遷移的優惠。

下列常見問題 (常見問題集) 涵蓋了建立虛擬機器供應項目的虛擬硬碟 (VHD) 和虛擬機器 (VM) 時，所遇到的常見問題。 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>如何使用上傳至進階儲存體的 VHD 從 Azure 入口網站建立虛擬機器？

Azure Marketplace 目前不支援從位於受控儲存體或 Azure 進階儲存體的映像，建立虛擬機器供應項目。  如需這些儲存體選項的詳細資訊，請參閱 [Azure 受控磁碟概觀](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。


## <a name="can-you-use-generation-2-vms-for-offers"></a>供應項目是否可使用第 2 代虛擬機器？

否，僅支援第 1 代的 VHD。  不過，我們目前正與 Microsoft Azure 平台小組共同合作，研究對第 2 代虛擬機器的支援。  如需進一步了解其中差異，請參閱[應該在 Hyper-V 中建立第 1 代還是第 2 代的虛擬機器？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)(英文)


## <a name="how-do-you-change-the-name-of-the-host"></a>如何變更主機的名稱？

無法變更。  建立虛擬機器之後，使用者 (包括擁有者) 即無法更新主機的名稱。


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何重設遠端桌面服務或其登入密碼？

下列文章說明如何針對以 Windows 和 Linux 為基礎的虛擬機器，執行 RDS 重設：   

- [如何在 Windows VM 中重設遠端桌面服務或其登入密碼](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [如何使用 VMAccess 擴充功能重設 Linux VM 密碼或 SSH 金鑰、修正 SSH 設定，和檢查磁碟一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>如何產生新 SSH 憑證？

在[取得適用於您 VM 映像的共用存取簽章 URI](./cpp-get-sas-uri.md) 一文 (英文) 的[建立虛擬機器供應項目的技術資產](./cpp-create-technical-assets.md)章節中，會說明產生憑證的相關資訊。


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何設定搭配我的虛擬機器使用的虛擬私人網路 (VPN)？

如果您使用的是 Azure Resource Manager 部署模型，則可選擇以下三種設定 VPN 的常用選項：
- [使用 Azure 門戶建立基於路由的 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [使用 PowerShell 來建立路由型 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [使用 CLI 來建立路由型 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Microsoft 對 Azure 型虛擬機器上執行的 Microsoft 伺服器軟體採取何種支援政策？

這些支援政策詳述於 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)一文。


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虛擬機器是否具有相關聯的唯一識別碼？

是，裝載於 Azure 時。  Azure 會為每個新建立的虛擬機器資源，指派名為「Azure 虛擬機器唯一識別碼」的唯一識別碼。  如需詳細資訊，請參閱 [Azure 虛擬機器唯一識別碼](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)部落格文章。  您亦可透過[清單 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)，以程式設計方式取得此識別碼。


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中，如何於啟動工作內管理自訂指令碼擴充功能？

下列文章詳細說明如何透過 Azure PowerShell 模組、Azure Resource Manager 範本使用自訂指令碼擴充功能，並詳述 Windows 系統上的疑難排解步驟：[自訂適用於 Windows 的指令碼擴充功能](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Azure Marketplace 可否支援 32 位元應用程式或服務？

一般而言不行。  適用於 Azure 虛擬機器的支援作業系統和標準服務，皆為 64 位元。  不過就技術觀點而言，大部分的 64 位元作業系統皆支援執行 32 位元版本應用程式，以達到回溯相容性。  不過，將 32 位元應用程式做為虛擬機器解決方案一部分的使用方式並不支援，因此*強烈建議避免如此*。  請改為將應用程式重新編譯為 64 位元專案。

如需詳細資訊，請參閱下列文章：
- [執行 32 位元應用程式](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (英文)
- [在 Azure 虛擬機器中的 32 位元作業系統支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>每當嘗試從我的 VHD 建立映像時，PowerShell 皆會顯示錯誤`.VHD is already registered with image repository as the resource`。 我之前未建立任何映像，也在 Azure 中找不到任何具有這個名稱的映像。 如何解決此問題？

如果使用者是從具有鎖定的 VHD 佈建虛擬機器，通常會發生此問題。  確認未從此 VHD 配置任何虛擬機器，然後重試此作業。  如果此問題持續發生，請開啟支援票證，如 [Cloud Partner 入口網站支援](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)中所述。 

