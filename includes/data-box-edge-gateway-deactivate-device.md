---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128871"
---
若要重設您的裝置，您需要安全地抹除您裝置的資料磁片和開機磁片上的所有資料。 

使用`Reset-HcsAppliance` Cmdlet 來清除資料磁片和開機磁片，或只抹除資料磁片。 `ClearData`和`BootDisk`參數可讓您分別抹除資料磁片和開機磁碟機。

`BootDisk`交換器會抹除開機磁片，並使裝置無法使用。 只有在需要將裝置傳回給 Microsoft 時，才應該使用此檔案。 如需詳細資訊，請參閱[將裝置返回 Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)。

如果您在本機 web UI 中使用裝置重設，則只會安全地抹除資料磁片，但是開機磁片會保持不變。 開機磁片包含裝置設定。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 在命令提示字元中，輸入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下列範例顯示如何使用這個 Cmdlet：

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
