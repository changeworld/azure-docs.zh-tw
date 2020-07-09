---
title: 在使用 Azure Site Recovery 遷移至 Azure 之後，設定嚴重損壞修復
description: 本文說明如何準備機器，以在使用 Azure Site Recovery 移轉至 Azure 之後，設定 Azure 區域之間的災害復原。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a71e476a214c44514c7d57c54a09a38218ad6d2a
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135672"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>移轉至 Azure 之後，設定 Azure VM 的災害復原 


如果您已使用[Site Recovery](site-recovery-overview.md)服務將內部[部署機器遷移至 Azure vm](./migrate-tutorial-on-premises-azure.md) ，而且您現在想要將 vm 設定為進行嚴重損壞修復到次要 Azure 區域，請遵循這篇文章。 本文說明如何確保 Azure VM 代理程式已安裝在已遷移的 Vm 上，以及如何移除在遷移後不再需要的 Site Recovery 行動服務。



## <a name="verify-migration"></a>確認遷移

設定災害復原之前，請確定移轉已如預期般完成。 若要成功完成移轉，您就要在容錯移轉之後，針對您需要移轉的每部機器，選取 [完成移轉]**** 選項。 

## <a name="verify-the-azure-vm-agent"></a>確認 Azure VM 代理程式

每個 Azure VM 都必須安裝[AZURE vm 代理程式](../virtual-machines/extensions/agent-windows.md)。 若要複寫 Azure Vm，Site Recovery 在代理程式上安裝擴充功能。

- 如果機器執行的是 Site Recovery 行動服務的9.7.0.0 開始或更新版本，則 Windows Vm 上的行動服務會自動安裝 Azure VM 代理程式。 在舊版的行動服務中，您將會手動安裝代理程式。
- 針對 Linux Vm，您必須手動安裝 Azure VM 代理程式。 僅在移轉後的機器上安裝之行動服務是 9.6 版或更早版本時，才需要安裝 Azure VM 代理程式。


### <a name="install-the-agent-on-windows-vms"></a>在 Windows Vm 上安裝代理程式

如果您執行的 Site Recovery 行動服務版本早于9.7.0.0 開始，或者您有其他需要手動安裝代理程式，請執行下列動作：  

1. 請確定您具有 VM 的系統管理許可權。
2. 下載[VM 代理程式安裝程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
3. 執行安裝程式檔案。

#### <a name="validate-the-installation"></a>驗證安裝
若要檢查是否已安裝代理程式：

1. 在 Azure VM 的 C:\WindowsAzure\Packages 資料夾中，您應該會看到 WaAppAgent.exe 檔案。
2. 在該檔案上按一下滑鼠右鍵，並在 [屬性]**** 中選取 [詳細資料]**** 索引標籤。
3. 確認 [產品版本]**** 欄位顯示 2.6.1198.718 或更高版本。

[深入瞭解](../virtual-machines/extensions/agent-windows.md)適用于 Windows 的代理程式安裝。

### <a name="install-the-agent-on-linux-vms"></a>在 Linux Vm 上安裝代理程式

手動安裝[Azure LINUX VM](../virtual-machines/extensions/agent-linux.md)代理程式，如下所示：

1. 請確定您在電腦上具有系統管理員許可權。
2. 強烈建議您從散發套件存放庫使用 RPM 或 DEB 套件來安裝 Linux VM 代理程式。 所有[認可的散發套件提供者](../virtual-machines/linux/endorsed-distros.md)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。
    - 我們強烈建議您只透過散發套件存放庫更新代理程式。
    - 我們不建議您直接從 GitHub 安裝 Linux VM 代理程式並加以更新。
    -  如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 

#### <a name="validate-the-installation"></a>驗證安裝 

1. 執行此命令： **ps-e** ，以確保 Azure 代理程式正在 Linux VM 上執行。
2. 如果此程序不在執行中，請使用下列命令來重新啟動它：
    - 適用于 Ubuntu：**服務 walinuxagent 啟動**
    - 針對其他散發套件：**服務 waagent 啟動**


## <a name="uninstall-the-mobility-service"></a>將行動服務解除安裝

1. 使用下列其中一種方法，從 Azure VM 手動卸載行動服務。 
    - 對於 Windows，請在 [控制台] > [新增/移除程式]**** 中，將 **Microsoft Azure Site Recovery 行動服務/主要目標伺服器**解除安裝。 在提升權限的命令提示字元上，執行：
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - 若為 Linux，請以根使用者身分登入。 在終端機中，移至 **/user/local/ASR**，然後執行下列命令：
        ```
        ./uninstall.sh -Y
        ```
2. 設定複寫之前，請將 VM 重新啟動。

## <a name="next-steps"></a>後續步驟

請參閱 Azure VM 代理程式上的 Site Recovery 延伸模組[疑難排解](site-recovery-extension-troubleshoot.md)。
將 Azure VM [快速複寫](azure-to-azure-quickstart.md)到次要地區。
