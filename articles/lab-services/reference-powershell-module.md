---
title: 適用于 Azure 實驗室服務的 PowerShell 模組 |Microsoft Docs
description: 本文提供 PowerShell 模組的相關資訊，協助您管理 Azure 實驗室服務中的構件。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6f5809dcf981a1ecf30cda06af03d2b8a06d3694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078823"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell 模組 (預覽)
Az. LabServices 是可簡化 Azure 實驗室服務管理的 PowerShell 模組。 它提供可組合的函式，以建立、查詢、更新和刪除實驗室帳戶、實驗室、Vm 和映射。 如需此課程模組的詳細資訊，請參閱 [GitHub 上的 Az. LabServices 首頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

> [!NOTE]
> 此課程模組目前為 **預覽**狀態。 

## <a name="example-command"></a>範例命令
以下範例說明如何使用 PowerShell 命令，在所有實驗室中停止所有執行中的 Vm。

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>開始使用
1. 如果電腦上不存在，請安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 。 
2. 將 [Az. LabServices. .psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) 下載到您的電腦。
3. 匯入模組：

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 執行下列命令來列出您訂用帳戶中的所有實驗室。

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>接下來的步驟
請參閱 [GitHub 上的 Az. LabServices 首頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。
