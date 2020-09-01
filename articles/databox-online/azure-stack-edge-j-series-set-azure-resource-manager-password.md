---
title: 在 Azure Stack Edge 裝置上設定 Azure Resource Manager 密碼
description: 說明如何使用 Azure PowerShell 連接到 Azure Stack Edge 上執行的 Azure Resource Manager。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2020
ms.author: alkohli
ms.openlocfilehash: d56f12c746383576a32e2c0ade542bc8aedff22d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083773"
---
# <a name="set-azure-resource-manager-password"></a>設定 Azure Resource Manager 密碼

[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]

本文說明如何設定您的 Azure Resource Manager 密碼。 當您透過 Azure Resource Manager 連接到裝置本機 Api 時，必須設定此密碼。

設定密碼的程式可能會根據您使用的是 Azure 入口網站或 PowerShell Cmdlet 而有所不同。 下列各節將說明每個程式。


## <a name="reset-password-via-the-azure-portal"></a>透過 Azure 入口網站重設密碼

1. 在 Azure 入口網站中，移至您所建立的 Azure Stack Edge 資源來管理您的裝置。 移至 [Edge 計算] > [開始使用]。

2. 在右窗格中，從命令列選取 [ **重設 EDGE ARM 密碼**]。 

    ![重設 EdgeARM 使用者密碼1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. 在 [ **重設 EdgeArm 使用者密碼** ] 分頁中，提供密碼以透過 Azure Resource Manager 連接到您的裝置本機 api。 確認密碼，然後選取 [ **重設**]。

    ![重設 EdgeARM 使用者密碼2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>透過 PowerShell 重設密碼

1. 在 Azure 入口網站中，移至您所建立用來管理裝置的 Azure Stack Edge 資源。 請記下 [ **總覽** ] 頁面中的下列參數。

    - Azure Stack Edge 資源名稱
    - 訂用帳戶識別碼

2. 移至 [ **設定] > 屬性**。 請記下 [ **屬性** ] 頁面中的下列參數。

    - 資源群組
    - CIK 加密金鑰：選取 [view]，然後複製 **加密金鑰**。

    ![取得 CIK 加密金鑰](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. 識別您將用來連接到 Azure Resource Manager 的密碼。

4. 啟動 cloud shell。 選取右上角的圖示：

    ![啟動 cloud shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Cloud shell 啟動後，您可能需要切換至 PowerShell。

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. 設定內容。 輸入：

    `Set-AzContext -SubscriptionId <Subscription ID>`

    以下是範例輸出：

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  如果您有任何舊的 PS 模組，則需要安裝這些模組。

    `Remove-Module  Az.DataBoxEdge -force`

    以下是範例輸出。 在此範例中，沒有要安裝的舊模組。

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. 下一組命令會下載並執行腳本，以安裝 PowerShell 模組。
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. 在下一組命令中，您必須提供資源名稱、資源組名、加密金鑰，以及您在上一個步驟中所識別的密碼。

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    密碼和加密金鑰參數必須以安全字串的形式傳遞。 使用下列 Cmdlet，將密碼和加密金鑰轉換為安全字串。

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    使用上述產生的安全字串做為 AzDataBoxEdgeUser Cmdlet 中的參數，以重設密碼。 使用您在建立 Azure Stack Edge/資料箱閘道資源時所使用的相同資源群組。

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    以下是範例輸出。
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
使用新密碼連接到 Azure Resource Manager。

## <a name="next-steps"></a>後續步驟

[連接到 Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)