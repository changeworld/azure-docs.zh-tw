---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174438"
---
如果您遇到任何裝置問題，您可以從系統記錄檔建立支援封裝。 Microsoft 支援服務會使用此套件來對問題進行疑難排解。 請遵循下列步驟來建立支援套件：

1. [連接到您裝置的 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsNodeSupportPackage` 命令來建立支援封裝。 Cmdlet 的使用方式如下：

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    此 Cmdlet 會從您的裝置收集記錄，並將這些記錄複製到指定的網路或本機共用。

    使用的參數如下所示：

    - `-Path` -指定要複製支援封裝的網路或本機路徑。 (必要)
    - `-Credential` -指定要存取受保護路徑的認證。
    - `-Zip` -指定以產生 zip 檔案。
    - `-Include` -指定要包含支援封裝中包含的元件。 如果未指定， `Default` 則會假設為。
    - `-IncludeArchived` -指定將封存的記錄包含在支援封裝中。
    - `-IncludePeriodicStats` -指定在支援封裝中包含週期性 stat 記錄。

    
