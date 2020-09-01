---
title: 在存取 Azure Stack Edge GPU 裝置的 Windows 用戶端上設定 TLS 1。2
description: 說明如何在存取 Azure Stack Edge GPU 裝置的 Windows 用戶端上設定 TLS 1.2。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: fdd88a902e812c89270a175aef4bf0de197c6e9b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146125"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>在存取 Azure Stack Edge 裝置的 Windows 用戶端上設定 TLS 1。2

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

如果您使用 Windows 用戶端來存取 Azure Stack Edge 裝置，您必須在用戶端上設定 TLS 1.2。 本文提供在 Windows 用戶端上設定 TLS 1.2 的資源和指導方針。 

此處提供的指導方針是以執行 Windows Server 2016 之用戶端上執行的測試為基礎。

## <a name="configure-tls-12-for-current-powershell-session"></a>為目前的 PowerShell 會話設定 TLS 1。2

請執行下列步驟，在您的用戶端上設定 TLS 1.2。

1. 以系統管理員身分執行 PowerShell。
2. 若要為目前的 PowerShell 會話設定 TLS 1.2，請輸入：
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>在用戶端上設定 TLS 1。2

如果您想要為您的環境設定全系統的 TLS 1.2，請遵循這些檔中的指導方針：

- [一般-如何啟用 TLS 1。2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [如何在用戶端啟用 TLS 1.2](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [如何在站台伺服器與遠端站台系統啟用 TLS 1.2](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL (Schannel SSP) 的通訊協定 ](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [加密套件](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)：特別設定 [TLS 加密套件順序](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) ，請確定您列出目前的加密套件，並在下列清單中遺漏任何遺漏：

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    您也可以直接編輯登錄設定來新增這些加密套件。

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- 如何設定橢圓形曲線

    請確定您列出目前的橢圓曲線，並在下列清單中遺漏任何遺漏：

    - P-256 
    - P-384

    您也可以直接編輯登錄設定來新增這些橢圓曲線。
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [將 [最小 RSA 金鑰交換大小] 設定為 2048](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)。



## <a name="next-steps"></a>接下來的步驟

[連接到 Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)