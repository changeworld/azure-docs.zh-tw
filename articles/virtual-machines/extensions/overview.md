---
title: Azure 虛擬機器擴充功能和功能
description: 深入瞭解 Azure VM 擴充功能
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: 73738f339bea3e8e075530e5de564c1c0854d283
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552045"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 虛擬機器擴充功能和功能
延伸模組是小型應用程式，可在 Azure Vm 上提供部署後設定和自動化。 Azure 平臺裝載許多延伸模組，涵蓋了 VM 設定、監視、安全性和公用程式。 發行者會採用應用程式、將它包裝成擴充功能，並簡化安裝。 您只需要提供強制參數。 

## <a name="how-can-i-find-what-extensions-are-available"></a>如何找到可用的擴充功能？
您可以選取 VM，然後選取左側功能表中的 [**擴充**功能]，以查看可用的擴充功能。 若要提取完整的延伸模組清單，請參閱[探索適用于 Linux 的 Vm 擴充](features-linux.md)功能和[探索適用于 Windows 的 vm 擴充](features-windows.md)功能。

## <a name="how-can-i-install-an-extension"></a>如何安裝擴充功能？
您可以使用 Azure CLI、PowerShell、Resource Manager 範本和 Azure 入口網站來管理 Azure VM 擴充功能。 若要嘗試延伸模組，請移至 Azure 入口網站，選取自訂腳本擴充功能，然後傳入命令或腳本來執行延伸模組。

如需詳細資訊，請參閱[Windows 自訂腳本擴充](custom-script-windows.md)功能和[Linux 自訂腳本延伸](custom-script-linux.md)模組。

## <a name="how-do-i-manage-extension-application-lifecycle"></a>如何管理擴充功能應用程式週期？
您不需要直接連線到 VM 來安裝或刪除延伸模組。 Azure 延伸模組生命週期會在 VM 之外進行管理，並整合到 Azure 平臺中。

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>對於擴充功能，我還應該考慮其他任何事項嗎？
有些個別的 VM 擴充功能應用程式可能會有自己的環境必要條件，例如對端點的存取。 每個擴充功能都有一篇文章，說明任何必要條件，包括支援的作業系統。

## <a name="troubleshoot-extensions"></a>針對擴充功能進行疑難排解

您可以在延伸模組總覽的**疑難排解和支援**一節中找到每個擴充功能的疑難排解資訊。 以下是可用的疑難排解資訊清單：

| 命名空間 | 疑難排解 |
|-----------|-----------------|
| dependencyagent. dependencyagentlinux。 | [適用于 Linux 的 Azure 監視器相依性](agent-dependency-linux.md#troubleshoot-and-support) |
| dependencyagent. dependencyagentwindows。 | [Windows Azure 監視器相依性](agent-dependency-windows.md#troubleshoot-and-support) |
| azurediskencryptionforlinux。 | [適用于 Linux 的 Azure 磁碟加密](azure-disk-enc-linux.md#troubleshoot-and-support) |
| azurediskencryption。 | [Windows 適用的 Azure 磁碟加密](azure-disk-enc-windows.md#troubleshoot-and-support) |
| customscriptextension | [適用于 Windows 的自訂腳本](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux. customscriptforlinux | [適用于 Linux 的 Desired State Configuration](dsc-linux.md#troubleshoot-and-support) |
| microsoft. powershell. dsc | [適用于 Windows 的 Desired State Configuration](dsc-windows.md#troubleshoot-and-support) |
| hpccompute. nvidiagpudriverlinux | [適用於 Linux 的 NVIDIA GPU 驅動程式擴充功能](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| hpccompute. nvidiagpudriverwindows | [適用于 Windows 的 NVIDIA GPU 驅動程式擴充功能](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| iaasantimalware。 | [Windows 的反惡意程式碼擴充功能](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring. monitoring. omsagentforlinux | [適用于 Linux 的 Azure 監視器](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring. monitoring. microsoftmonitoringagent | [適用于 Windows 的 Azure 監視器](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. stackifylinuxageNtextension | [適用于 Linux 的 Stackify 回溯](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. microsoft.ostcextensions.customscriptforlinux | [重設 Linux 的密碼](vmaccess.md#troubleshoot-and-support) |
| azurerm.recoveryservices. vmsnapshot | [Linux 的快照集](vmsnapshot-linux.md#troubleshoot-and-support) |
| azurerm.recoveryservices. vmsnapshot | [Windows 的快照集](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>後續步驟
* 如需 Linux 代理程式和擴充功能如何使用的詳細資訊，請參閱[適用于 linux 的 AZURE VM 延伸模組和功能](features-linux.md)。
* 如需 Windows 來賓代理程式和擴充功能如何使用的詳細資訊，請參閱[適用于 windows 的 AZURE VM 延伸模組和功能](features-windows.md)。  
* 若要安裝 Windows 來賓代理程式，請參閱[Azure Windows 虛擬機器代理程式總覽](agent-windows.md)。  
* 若要安裝 Linux 代理程式，請參閱[Azure Linux 虛擬機器代理程式總覽](agent-linux.md)。  

