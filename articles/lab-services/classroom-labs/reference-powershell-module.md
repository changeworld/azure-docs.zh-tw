---
title: 用於 Azure 實驗室服務的 PowerShell 模組 |微軟文檔
description: 本文提供有關 PowerShell 模組的資訊，該模組可説明管理 Azure 實驗室服務中的專案。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609394"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell 模組 (預覽)
Az.LabServices 是一個 PowerShell 模組，可簡化 Azure 實驗室服務的管理。 它提供了可組合功能來創建、查詢、更新和刪除實驗室帳戶、實驗室、VM 和圖像。 有關此模組的詳細資訊，請參閱[GitHub 上的 Az.LabServices 主頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

> [!NOTE]
> 此模組處於**預覽版**。 

## <a name="example-command"></a>範例命令
下面是使用 PowerShell 命令停止所有實驗室中所有正在運行的 VM 的示例。

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>開始使用
1. 如果[電腦上不存在 Azure PowerShell，](https://docs.microsoft.com/powershell/azure/overview)請安裝它。 
2. 將[Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1)下載到您的電腦。
3. 導入模組：

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 運行以下命令以列出訂閱中的所有實驗室。

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>後續步驟
請參閱[GitHub 上的 Az.LabServices 主頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。
