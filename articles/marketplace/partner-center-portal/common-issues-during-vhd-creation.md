---
title: VHD 建立過程中的常見問題 (常見問題集)
description: 在創建虛擬硬碟 (VHD) 時,經常詢問的常見問題。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266262"
---
# <a name="common-issues-during-vhd-creation"></a>VHD 建立過程中的常見問題

> [!NOTE]
> 我們將 Azure VM 產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請繼續按照雲合作夥伴門戶中的[VHD 創建 (FAQ) 中常見問題](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)中的說明進行管理您的產品/服務。

這些常見問題 (FAQ) 涵蓋了為 Azure 虛擬機器產品創建虛擬硬碟 (VHD) 時可能會遇到的常見問題。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>如何使用進階儲存的 VHD 從 Azure 門戶創建 VM?

Azure 應用商店當前不支援從託管存儲或 Azure 進階儲存上的影像創建 VM 產品/ 有關詳細資訊,請參閱[Azure 託管磁碟概述](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

## <a name="can-i-use-generation-2-vms-for-offers"></a>我可以使用第 2 代 VM 進行優惠嗎?

否,僅支援第 1 代 VHD。 但是,我們當前正在與 Microsoft Azure 平台團隊合作,調查對第 2 代 VM 的支援。 有關詳細資訊,請參閱[是否應在 Hyper-V 中創建第 1 代或第 2 代虛擬機器?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>如何變更主機的名稱？

您無法開啟。 創建 VM 後,使用者(包括擁有者)無法更新主機名。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>如何重置遠端桌面服務或其登錄密碼?

這些文章解釋了如何對基於 Windows 和 Linux 的 VM 執行 RDS 重置:

* [如何在 Windows VM 中重設遠端桌面服務或其登入密碼](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [如何使用 VMAccess 擴充功能重設 Linux VM 密碼或 SSH 金鑰、修正 SSH 設定，和檢查磁碟一致性](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>如何生成新的 SSH 證書?

證書的生成在[Azure VM 映像認證](https://aks.ms/CertifyVMimage)中解釋。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何配置虛擬專用網路 (VPN) 以使用 VM?

如果使用 Azure 資源管理員部署模型,則有三個選項:

* [使用 Azure 門戶建立基於路由的 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [使用 Azure PowerShell 建立基於路由的 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [使用 CLI 來建立路由型 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Microsoft 對 Azure 型虛擬機器上執行的 Microsoft 伺服器軟體採取何種支援政策？

您可以在微軟 Azure[虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)中找到詳細資訊。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>虛擬機器是否具有相關聯的唯一識別碼？

是，裝載於 Azure 時。 Azure 將唯一識別碼(稱為[Azure 虛擬機器代碼)](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)分配給創建的每個新 VM 資源。 有關詳細資訊,請參閱 Azure 虛擬機器唯一 ID。 您還可以透過[清單 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)取得此識別碼。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中,如何管理啟動任務中的自訂文稿擴展?

有關使用 Azure PowerShell 模組、Azure 資源管理員樣本以及 Windows 系統上的故障排除步驟使用自訂文稿延伸的詳細資訊,請參閱[Windows 的自訂文稿擴展](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure 應用商店中是否支援 32 位元應用程式或服務?

一般而言不行。 適用於 Azure 虛擬機器的支援作業系統和標準服務，皆為 64 位元。 雖然大多數 64 位元作業系統都支援 32 位元版本的應用程式,以便向後相容,但不支援並且強烈建議使用 32 位元應用程式作為 VM 解決方案的一部分。 將應用程式重新創建為 64 位元專案。

如需詳細資訊，請參閱這些文章：

* [執行 32 位元應用程式](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (英文)
* [在 Azure 虛擬機器中的 32 位元作業系統支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>錯誤:VHD 已註冊到映像儲存函式庫作為資源

每次嘗試從 VHD 創建映射時,都會在 Azure PowerShell 中收到錯誤「VHD 已在映射存儲庫中註冊為資源」。。 我以前沒有創建任何圖像,也沒有在 Azure 中找到任何具有此名稱的圖像。 如何解決這個問題？

如果從具有鎖定的 VHD 創建 VM,則通常會出現此問題。 確認沒有從此 VHD 分配 VM,然後重試該操作。 如果此問題仍然存在,則打開支援票證。 請參考[合作夥伴中心的支援](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)。
