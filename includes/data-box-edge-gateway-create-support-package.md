---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174438"
---
如果遇到任何設備問題，可以從系統日誌創建支援包。 Microsoft 支援使用此包來解決問題。 按照以下步驟創建支援包：

1. [連接到設備的 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsNodeSupportPackage`命令創建支援包。 Cmdlet 的用法如下：

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

    Cmdlet 從您的設備收集日誌，並將這些日誌複製到指定的網路或本地共用。

    使用的參數如下：

    - `-Path`- 指定要將支援包複製到的網路或本地路徑。 (必要)
    - `-Credential`- 指定訪問受保護路徑的憑據。
    - `-Zip`- 指定以生成 ZIP 檔案。
    - `-Include`- 指定以包括要包含在支援包中的元件。 如果未指定，`Default`則假定為。
    - `-IncludeArchived`- 指定在支援包中包含存檔日誌。
    - `-IncludePeriodicStats`- 指定在支援包中包括定期統計日誌。

    
