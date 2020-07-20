---
title: 在 Azure Key Vault 中建立和合併 CSR
description: 在 Azure Key Vault 中建立和合併 CSR
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: ae2d6259bac6a2034edc98de9b0405f32f17fbc3
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849490"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>在 Key Vault 中建立和合併 CSR

Azure Key Vault 支援將您所選任何憑證授權單位發行的數位憑證儲存在金鑰保存庫中。 其支援建立具有私人/公開金鑰組的憑證簽署要求，這可由任何任何指定的憑證授權單位簽署。 其可以是內部企業 CA 或外部公用 CA。 憑證簽署要求 (也就是 CSR 或認證要求) 是由使用者傳送到憑證授權單位 (CA) 的訊息，目的是要求發行憑證。

如需更多有關憑證的一般資訊，請參閱 [Azure Key Vault 憑證](/azure/key-vault/certificates/about-certificates)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>在 Key Vault 中新增非信任 CA 所發行的憑證

下列步驟將協助您從非 Key Vault 合作憑證授權單位中建立憑證 (例如，GoDaddy 不是受信任的金鑰保存庫 CA) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  首先，**建立憑證原則**。 Key Vault 不會代表使用者註冊或更新該簽發者的憑證，因為在此案例中選擇的 CA 未受支援，因此 IssuerName 設定為 [未知]。

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. 建立**憑證簽署要求**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. 取得 **CA 簽署的 CSR 要求** `$certificateOperation.CertificateSigningRequest` 是憑證的 base4 編碼憑證簽署要求。 您可以採用此 Blob，並傾印到簽發者的憑證要求網站。 此步驟會因 CA 而異，最好的方式就是查閱 CA 指導方針來了解如何執行此步驟。 您也可以使用 certreq 或 openssl 之類的工具來取得已簽署的憑證要求，並完成憑證產生程序。


4. **在 Key Vault 中合併已簽署的要求**，在簽發者簽署憑證要求之後，您可以帶回已簽署的憑證，並將其與一開始在 Azure Key Vault 中建立的私人/公開金鑰組合併

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    現在已成功合併憑證要求。

### <a name="azure-portal"></a>Azure 入口網站

1.  若要為您選擇的 CA 產生 CSR，請瀏覽至您想要新增憑證的金鑰保存庫。
2.  在 Key Vault 屬性頁面上，選取 [憑證]。
3.  選取 [產生/匯入] 索引標籤。
4.  在 [建立憑證] 畫面上，選擇下列值：
    - **憑證建立方法：** 產生。
    - **憑證名稱：** ContosoManualCSRCertificate。
    - **憑證授權單位 (CA) 的類型：** 非整合式 CA 所發行的憑證
    - **主旨：** `"CN=www.contosoHRApp.com"`
    - 視需要選取其他值。 按一下 [建立]。

    ![憑證屬性](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  您會看到憑證現在已新增至 [憑證] 清單中。 選取您剛才建立的新憑證。 憑證的目前狀態會是「已停用」，因為 CA 尚未發行該憑證。
7. 按一下 [憑證作業] 索引標籤，然後選取 [下載 CSR]。
 ![憑證屬性](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  將 .csr 檔案帶到 CA，以簽署要求。
9.  一旦 CA 簽署要求之後，請將憑證檔案帶回，以在同一個憑證作業畫面中**合併已簽署的要求**。

現在已成功合併憑證要求。

## <a name="troubleshoot"></a>疑難排解

如果在 Azure 入口網站中發出的憑證處於「已停用」狀態，請繼續檢視**憑證作業**，來檢閱該憑證的錯誤訊息。

如需詳細資訊，請參閱 [Key Vault REST API 參考中的憑證作業](/rest/api/keyvault)。 如需建立權限的相關資訊，請參閱[保存庫 - 建立或更新](/rest/api/keyvault/vaults/createorupdate)和[保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>後續步驟

- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
