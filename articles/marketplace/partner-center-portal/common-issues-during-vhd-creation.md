---
title: VHD 建立過程中的常見問題 (常見問題集)
description: 建立虛擬硬碟（VHD）時常見問題的常見問題。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266262"
---
# <a name="common-issues-during-vhd-creation"></a>VHD 建立期間的常見問題

> [!NOTE]
> 我們正在將您 Azure VM 供應專案的管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請繼續遵循在 Cloud Partner 入口網站中的[VHD 建立（FAQ）中常見問題](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)的指示，以管理您的供應專案。

這些常見問題（FAQ）涵蓋您在建立 Azure 虛擬機器供應專案的虛擬硬碟（VHD）時可能會遇到的常見問題。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>如何? 使用 premium 儲存體中的 VHD 從 Azure 入口網站建立 VM 嗎？

Azure Marketplace 目前不支援從受控儲存體或 Azure 進階儲存體的映射建立 VM 供應專案。 如需詳細資訊，請參閱[Azure 受控磁碟總覽](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

## <a name="can-i-use-generation-2-vms-for-offers"></a>我可以使用第2代 Vm 來提供供應專案嗎？

否，只支援第1代 Vhd。 不過，我們目前正與 Microsoft Azure 平臺小組合作，調查第2代 Vm 的支援。 如需詳細資訊，請參閱[我應該在 hyper-v 中建立第1代或第2代虛擬機器嗎？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>如何變更主機的名稱？

您無法開啟。 建立 VM 之後，使用者（包括擁有者）就無法更新主機名稱。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何? 重設遠端桌面服務或其登入密碼？

這些文章說明如何針對以 Windows 和 Linux 為基礎的 Vm 執行 RDS 重設：

* [如何在 Windows VM 中重設遠端桌面服務或其登入密碼](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [如何使用 VMAccess 擴充功能重設 Linux VM 密碼或 SSH 金鑰、修正 SSH 設定，和檢查磁碟一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>如何? 產生新的 SSH 憑證？

[AZURE VM 映射認證](https://aks.ms/CertifyVMimage)中會說明產生憑證。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何? 設定虛擬私人網路（VPN）來與我的 Vm 搭配使用嗎？

如果您使用 Azure Resource Manager 部署模型，您有三個選項：

* [使用 Azure 入口網站建立以路由為基礎的 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [使用 Azure PowerShell 建立以路由為基礎的 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [使用 CLI 來建立路由型 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Microsoft 對 Azure 型虛擬機器上執行的 Microsoft 伺服器軟體採取何種支援政策？

您可以在[Microsoft 伺服器軟體支援中找到 Microsoft Azure 虛擬機器的](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)詳細資料。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虛擬機器是否具有相關聯的唯一識別碼？

是，裝載於 Azure 時。 Azure 會為每個建立的新 VM 資源指派一個唯一識別碼，稱為「 [Azure 虛擬機器」唯一 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)。 如需詳細資訊，請參閱 Azure 虛擬機器唯一識別碼。 您也可以透過[清單 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)取得此識別碼。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中，如何管理啟動工作中的自訂腳本擴充功能？

如需使用 Azure PowerShell 模組的自訂腳本擴充功能、Azure Resource Manager 範本，以及 Windows 系統上的疑難排解步驟的詳細資訊，請參閱[windows 的自訂腳本擴充](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)功能。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace 中是否支援32位應用程式或服務？

一般而言不行。 適用於 Azure 虛擬機器的支援作業系統和標準服務，皆為 64 位元。 雖然大部分的64位作業系統都支援32位版本的應用程式以提供回溯相容性，但不支援使用32位應用程式作為 VM 解決方案的一部分，且非常不鼓勵。 將您的應用程式重新建立為64位專案。

如需詳細資訊，請參閱這些文章：

* [執行 32 位元應用程式](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (英文)
* [在 Azure 虛擬機器中的 32 位元作業系統支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>錯誤：已將 VHD 註冊為資源的映射存放庫

每次嘗試從我的 Vhd 建立映射時，都會收到錯誤「VHD 已在 Azure PowerShell 中以映射儲存機制註冊為資源」。 我在 Azure 中找不到任何使用此名稱的映射，也未建立任何映射。 如何解決這個問題？

如果您從具有鎖定的 VHD 建立 VM，通常會出現此問題。 確認未從這個 VHD 配置任何 VM，然後再次嘗試操作。 如果此問題持續發生，請開啟支援票證。 請參閱[合作夥伴中心的支援](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)。
