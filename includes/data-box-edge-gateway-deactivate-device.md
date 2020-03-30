---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128871"
---
要重置設備，您需要安全地擦除資料磁片和設備引導磁片上的所有資料。 

使用`Reset-HcsAppliance`Cmdlet 擦除資料磁片和引導磁片，或者僅清除資料磁片。 `ClearData`和`BootDisk`交換器允許您分別擦除資料磁片和引導磁片。

交換器`BootDisk`擦除引導磁片並使設備無法使用。 僅當設備需要返回到 Microsoft 時，才應使用它。 有關詳細資訊，請參閱[將設備返回到 Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)。

如果在本地 Web UI 中使用設備重置，則僅安全地擦除資料磁片，但引導磁片保持不變。 引導磁片包含設備配置。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 在命令提示字元中，輸入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下面的示例演示如何使用此 Cmdlet：

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
