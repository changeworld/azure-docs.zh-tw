---
title: 使用 Microsoft Azure Stack Hub 準備檢查工具建立憑證 |Microsoft Docs
description: 說明如何建立憑證要求，然後使用 Azure Stack Hub 的「準備就緒檢查工具」工具，在 Azure Stack Edge Pro GPU 裝置上取得並安裝憑證。
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 544625fe9fd2dbd87ad7330d7277494cbfbe6eb9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891093"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>使用 Azure Stack Hub 的就緒檢查程式工具建立 Azure Stack Edge Pro 的憑證 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明如何使用 Azure Stack Hub 的「準備就緒檢查工具」，為您的 Azure Stack Edge Pro 建立憑證。 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>使用 Azure Stack Hub 的準備檢查工具

使用 Azure Stack Hub 的「準備就緒檢查工具」來建立憑證簽署要求， (Csr) 進行 Azure Stack Edge Pro 裝置部署。 您可以在下一 Azure Stack Edge Pro 裝置的訂單並等待裝置抵達之前，建立這些要求。 

> [!NOTE]
> 此工具僅適用于測試或開發用途，不適用於生產裝置。 

您可以使用 Azure Stack Hub 整備檢查工具 (AzsReadinessChecker) 來要求下列憑證：

- Azure Resource Manager 憑證
- 本機 UI 憑證
- 節點憑證
- Blob 憑證
- VPN 憑證


## <a name="prerequisites"></a>必要條件

若要為 Azure Stack Edge Pro 裝置部署建立 Csr，請確定： 

- 您有執行 Windows 10 或 Windows Server 2016 或更新版本的用戶端。 
- 您已從這個系統上 [的 PowerShell 資源庫](https://aka.ms/AzsReadinessChecker) 下載 Microsoft Azure Stack Hub 1.2002.1133.85 檢查工具。 您可能需要搜尋此套件。 只有此版本的工具可以建立 Azure Stack Edge Pro 裝置的憑證。
- 您有下列憑證資訊：
  - 裝置名稱
  - 節點序號
  - 外部完整網域名稱 (FQDN)

## <a name="generate-certificate-signing-requests"></a>產生憑證簽署要求

使用下列步驟來準備 Azure Stack Edge Pro 裝置憑證：

1. 以系統管理員身分執行 PowerShell (5.1 或更新版本的) 。
2. 安裝 Azure Stack Hub 的就緒檢查工具。 在 PowerShell 提示字元中，輸入： 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker -RequiredVersion 1.2002.1133.85 -Force
    ```

    若要確認已安裝的版本，請輸入：  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. 為所有憑證建立目錄（如果不存在的話）。 輸入： 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. 若要建立憑證要求，請提供下列資訊。 如果您要產生 VPN 憑證，其中有些輸入不適用。 
    
    |輸入 |描述  |
    |---------|---------|
    |`OutputRequestPath`|您要在本機用戶端上建立憑證要求的檔案路徑。        |
    |`DeviceName`|裝置在裝置本機 web UI 的 [ **裝置** ] 頁面中的名稱。 <br> VPN 憑證不需要此欄位。         |
    |`NodeSerialNumber`|裝置本機 web UI 的 [ **網路** ] 頁面中裝置節點的序號。 <br> VPN 憑證不需要此欄位。       |
    |`ExternalFQDN`|裝置本機 web UI 的 [ **裝置** ] 頁面中的功能變數名稱值。         |
    |`RequestType`|要求類型可以是 `MultipleCSR` 不同端點的不同憑證，也可以是 `SingleCSR` 所有端點的單一憑證。 <br> VPN 憑證不需要此欄位。     |

    針對除了 VPN 憑證以外的所有憑證，請輸入： 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    如果您要建立 VPN 憑證，請輸入： 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. 您會在上述 OutputRequestPath 參數中指定的目錄下找到憑證要求檔案。 使用參數時 `MultipleCSR` ，您會看到4個副檔名為的檔案 `.req` 。 這些檔案如下所示：

    
    |檔案名稱  |憑證要求的類型  |
    |---------|---------|
    |從您的 `DeviceName`     |本機 web UI 憑證要求      |
    |從您的 `NodeSerialNumber`     |節點憑證要求         |
    |從 `login` 開始     |Azure Resource Manager 端點憑證要求       |
    |從 `wildcard` 開始     |Blob 儲存體憑證要求;它包含萬用字元，因為它涵蓋您可能在裝置上建立的所有儲存體帳戶。          |
    |從 `AzureStackEdgeVPNCertificate` 開始     |VPN 用戶端憑證要求。         |

    您也會看到 INF 資料夾。 這包含一項管理。 <邊緣-devicename> 資訊檔，以純文字說明憑證詳細資料。  


6. 將這些檔案提交給您的憑證授權單位單位， (內部或公用) 。 請確定您的 CA 會使用您產生的要求來產生憑證，以符合 [節點憑證](azure-stack-edge-j-series-manage-certificates.md#node-certificates)、 [端點憑證](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)和 [本機 UI 憑證](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates)的 Azure Stack Edge Pro 憑證需求。

## <a name="prepare-certificates-for-deployment"></a>準備憑證以進行部署

您從憑證授權單位單位取得的憑證檔案 (CA) 必須使用符合 Azure Stack Edge Pro 裝置的憑證需求的屬性來匯入和匯出。 在您產生憑證簽署要求的相同系統上，完成下列步驟。

- 若要匯入憑證，請依照 [存取 Azure Stack Edge Pro 裝置的用戶端上的匯入憑證](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步驟執行。

- 若要匯出憑證，請遵循 [從存取 Azure Stack Edge Pro 裝置的用戶端匯出憑證](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步驟。


## <a name="validate-certificates"></a>驗證憑證

首先，您將產生適當的資料夾結構，並將憑證放在對應的資料夾中。 只有當您使用工具驗證憑證。

1. 以系統管理員身分執行 PowerShell。

2. 若要產生適當的資料夾結構，請在提示中輸入：

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. 將 PFX 密碼轉換成安全字串。 輸入：       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. 接下來，驗證憑證。 輸入：

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>下一步

[部署 Azure Stack Edge Pro 裝置](azure-stack-edge-gpu-deploy-prep.md)
