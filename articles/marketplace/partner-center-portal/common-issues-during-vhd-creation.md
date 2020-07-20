---
title: VHD 建立過程中的常見問題 (常見問題集)
description: 建立虛擬硬碟 (VHD) 時常見問題的常見問題集。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: emuench
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: 2b6ab5d36cd5a1f66badc79d1b2d42e464d028f4
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110737"
---
# <a name="common-issues-during-vhd-creation"></a>VHD 建立過程中的常見問題

這些常見問題集 (FAQ) 涵蓋您在建立 Azure 虛擬機器供應項目的虛擬硬碟 (VHD) 時可能會遇到的常見問題。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>如何使用進階儲存體中的 VHD 從 Azure 入口網站建立 VM？

Azure Marketplace 目前不支援從受控儲存體或 Azure 進階儲存體的映像，建立 VM 供應項目。 如需詳細資訊，請參閱 [Azure 受控磁碟概觀](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="can-i-use-generation-2-vms-for-offers"></a>供應項目是否可使用第 2 代 VM？

否，僅支援第 1 代的 VHD。 不過，我們目前正與 Microsoft Azure 平台小組共同合作，研究對第 2 代 VM 的支援。 如需詳細資料，請參閱[應該在 Hyper-V 中建立第 1 代還是第 2 代的虛擬機器？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>如何變更主機的名稱？

您無法。 建立 VM 之後，使用者 (包括擁有者) 即無法更新主機名稱。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何重設遠端桌面服務或其登入密碼？

這些文章說明如何針對以 Windows 和 Linux 為基礎的虛擬機器，執行 RDS 重設：

* [如何在 Windows VM 中重設遠端桌面服務或其登入密碼](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [如何使用 VMAccess 擴充功能重設 Linux VM 密碼或 SSH 金鑰、修正 SSH 設定，和檢查磁碟一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>如何產生新的 SSH 憑證？

[Azure VM 映像認證](https://aks.ms/CertifyVMimage)會說明如何產生憑證。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何設定搭配我的 VM 使用之虛擬私人網路 (VPN)？

如果您使用的是 Azure Resource Manager 部署模型，則有以下三個選項：

* [使用 Azure 入口網站建立路由型 VPN 閘道](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [使用 Azure PowerShell 來建立路由型 VPN 閘道](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [使用 CLI 來建立路由型 VPN 閘道](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Microsoft 對 Azure 型虛擬機器上執行的 Microsoft 伺服器軟體採取何種支援政策？

您可以在[適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)中找到詳細資料。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虛擬機器是否具有相關聯的唯一識別碼？

是，裝載於 Azure 時。 Azure 會為每個新建立的 VM 資源，指派名為 [Azure 虛擬機器唯一識別碼](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)的唯一識別碼。 如需詳細資料，請參閱 Azure 虛擬機器唯一識別碼。 您也可以透過[清單 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list) 取得此識別碼。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中，如何在啟動工作中管理自訂指令碼延伸模組？

如需如何透過 Azure PowerShell 模組、Azure Resource Manager 範本以及 Windows 系統上的疑難排解步驟使用自訂指令碼延伸模組的詳細資料，請參閱[自訂適用於 Windows 的指令碼延伸模組](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace 是否支援 32 位元應用程式或服務？

一般而言不行。 適用於 Azure 虛擬機器的支援作業系統和標準服務，皆為 64 位元。 雖然大部分的 64 位作業系統都支援 32 位版本的應用程式以提供回溯相容性，但在 VM 解決方案中不支援使用 32 位應用程式，且非常不鼓勵。 將您的應用程式重新建立為 64 位元專案。

如需詳細資訊，請參閱這些文章：

* [執行 32 位元應用程式](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (英文)
* [在 Azure 的虛擬機器中的 32 位元作業系統的支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>錯誤：已向映像存放庫註冊 VHD 做為資源

每次嘗試從我的 VHD 建立映像時，都會在 Azure PowerShell 中收到錯誤「已向映像存放庫註冊 VHD 做為資源」。 我之前未建立任何映像，在 Azure 中也找不到任何具有這個名稱的映像。 如何解決這個問題？

如果您是從具有鎖定的 VHD 建立 VM，通常會出現此問題。 確認未從此 VHD 配置任何 VM，然後重試此作業。 如果持續發生此問題，請開啟一個支援票證。 請參閱[合作夥伴中心的支援](support.md)。
