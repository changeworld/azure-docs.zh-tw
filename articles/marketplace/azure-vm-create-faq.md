---
title: Azure Marketplace 中 VM 的常見問題
description: 在 Azure Marketplace 中建立虛擬機器時遇到的常見問題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 131cd9b1de82213d89ee07f30aed25544f6fcb18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283933"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Marketplace 中 VM 的常見問題

這些常見問題 (常見問題) 涵蓋建立虛擬機器時可能遇到的常見問題， (VM) 供應專案 Azure Marketplace。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>如何設定搭配我的 VM 使用之虛擬私人網路 (VPN)？

如果您使用的是 Azure Resource Manager 部署模型，則有以下三個選項：

- [使用 Azure 入口網站建立路由型 VPN 閘道](../vpn-gateway/create-routebased-vpn-gateway-portal.md)
- [使用 Azure PowerShell 來建立路由型 VPN 閘道](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [使用 CLI 來建立路由型 VPN 閘道](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Microsoft 對 Azure 型虛擬機器上執行的 Microsoft 伺服器軟體採取何種支援政策？

您可以在[適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)中找到詳細資料。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>在 VM 中，如何在啟動工作中管理自訂指令碼延伸模組？

如需如何透過 Azure PowerShell 模組、Azure Resource Manager 範本以及 Windows 系統上的疑難排解步驟使用自訂指令碼延伸模組的詳細資料，請參閱[自訂適用於 Windows 的指令碼延伸模組](/azure/virtual-machines/extensions/custom-script-windows)。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace 是否支援 32 位元應用程式或服務？

不正確。 適用於 Azure 虛擬機器的支援作業系統和標準服務，皆為 64 位元。 雖然大部分的 64 位作業系統都支援 32 位版本的應用程式以提供回溯相容性，但在 VM 解決方案中不支援使用 32 位應用程式，且非常不鼓勵。 將您的應用程式重新建立為 64 位元專案。

如需詳細資訊，請參閱這些文章：

- [執行 32 位元應用程式](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (英文)
- [在 Azure 的虛擬機器中的 32 位元作業系統的支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>錯誤：已向映像存放庫註冊 VHD 做為資源

每次嘗試從我的 VHD 建立映像時，都會在 Azure PowerShell 中收到錯誤「已向映像存放庫註冊 VHD 做為資源」。 我之前未建立任何映像，在 Azure 中也找不到任何具有這個名稱的映像。 如何解決這個問題？

如果您是從具有鎖定的 VHD 建立 VM，通常會出現此問題。 確認未從此 VHD 配置任何 VM，然後重試此作業。 如果持續發生此問題，請開啟一個支援票證。 請參閱[合作夥伴中心的支援](support.md)。

## <a name="next-steps"></a>後續步驟

- [VM 認證疑難排解](azure-vm-create-certification-faq.md)
