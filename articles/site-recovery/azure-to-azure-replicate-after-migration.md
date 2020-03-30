---
title: 使用 Azure 網站恢復在遷移到 Azure 後設置災害復原
description: 本文說明如何準備機器，以在使用 Azure Site Recovery 移轉至 Azure 之後，設定 Azure 區域之間的災害復原。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159106"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>移轉至 Azure 之後，設定 Azure VM 的災害復原 


如果使用[網站恢復](site-recovery-overview.md)服務[將本地電腦遷移到 Azure VM，](tutorial-migrate-on-premises-to-azure.md)並且現在希望將用於災害復原的 VM 設置為輔助 Azure 區域，請按照本文進行操作。 本文介紹如何確保 Azure VM 代理安裝在遷移的 VM 上，以及如何刪除遷移後不再需要的網站恢復移動服務。



## <a name="verify-migration"></a>驗證遷移

設定災害復原之前，請確定移轉已如預期般完成。 若要成功完成移轉，您就要在容錯移轉之後，針對您需要移轉的每部機器，選取 [完成移轉]**** 選項。 

## <a name="verify-the-azure-vm-agent"></a>驗證 Azure VM 代理

每個 Azure VM 都必須安裝[Azure VM 代理](../virtual-machines/extensions/agent-windows.md)。 要複製 Azure VM，網站恢復在代理上安裝擴展。

- 如果電腦正在運行網站恢復移動服務的版本 9.7.0.0 或更高版本，則 Windows VM 上的移動服務會自動安裝 Azure VM 代理。 在移動服務的早期版本上，您將手動安裝代理。
- 對於 Linux VM，必須手動安裝 Azure VM 代理。 僅在移轉後的機器上安裝之行動服務是 9.6 版或更早版本時，才需要安裝 Azure VM 代理程式。


### <a name="install-the-agent-on-windows-vms"></a>在 Windows VM 上安裝代理

如果您運行的網站恢復移動服務版本早于 9.7.0.0，或者您還需要手動安裝代理，請執行以下操作：  

1. 確保您對 VM 具有管理員許可權。
2. 下載[VM 代理安裝程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
3. 運行安裝程式檔。

#### <a name="validate-the-installation"></a>驗證安裝
要檢查是否安裝了代理：

1. 在 Azure VM 的 C:\WindowsAzure\Packages 資料夾中，您應該會看到 WaAppAgent.exe 檔案。
2. 在該檔案上按一下滑鼠右鍵，並在 [屬性]**** 中選取 [詳細資料]**** 索引標籤。
3. 確認 [產品版本]**** 欄位顯示 2.6.1198.718 或更高版本。

[詳細瞭解](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)Windows 的代理安裝。

### <a name="install-the-agent-on-linux-vms"></a>在 Linux VM 上安裝代理

手動安裝[Azure Linux VM](../virtual-machines/extensions/agent-linux.md)代理，如下所示：

1. 請確保您對電腦具有管理員許可權。
2. 我們強烈建議您使用分發的包存儲庫中的 RPM 或 DEB 包安裝 Linux VM 代理。 所有[認可的散發套件提供者](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。
    - 我們強烈建議您只透過散發套件存放庫更新代理程式。
    - 我們不建議直接從 GitHub 安裝 Linux VM 代理並更新它。
    -  如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 

#### <a name="validate-the-installation"></a>驗證安裝 

1. 運行此命令 **：ps-e**以確保 Azure 代理在 Linux VM 上運行。
2. 如果此程序不在執行中，請使用下列命令來重新啟動它：
    - 對於 Ubuntu：**服務華樂代理開始**
    - 對於其他分發：**服務外銷啟動**


## <a name="uninstall-the-mobility-service"></a>將行動服務解除安裝

1. 使用以下方法之一從 Azure VM 手動卸載移動服務。 
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

[查看](site-recovery-extension-troubleshoot.md)Azure VM 代理上的網站恢復擴展的故障排除。
將 Azure VM [快速複寫](azure-to-azure-quickstart.md)到次要地區。
