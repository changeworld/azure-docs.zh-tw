---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128871"
---
若要重設裝置，您需要安全地抹除裝置的資料磁碟和開機磁碟上的所有資料。 

使用 `Reset-HcsAppliance` Cmdlet 來抹除資料磁碟和開機磁碟，或只抹除資料磁碟。 `ClearData` 和 `BootDisk` 交換器可讓您分別抹除資料磁碟和開機磁碟。

`BootDisk` 交換器會抹除開機磁碟，並使裝置無法使用。 只有在需要將裝置傳回給 Microsoft 時，才應該使用。 如需詳細資訊，請參閱[將裝置傳回給 Microsoft Word](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)。

如果您在本機 Web UI 中使用裝置重設，則只會安全地抹除資料磁碟，但是開機磁碟會保持不變。 開機磁碟包含裝置組態。

1. [連線至 Powershell 介面](#connect-to-the-powershell-interface)。
2. 在命令提示字元中，輸入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下列範例將示範如何使用這個 Cmdlet：

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
