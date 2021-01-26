---
title: 在 Azure Key Vault 中建立和合併 CSR
description: 了解如何在 Azure Key Vault 中建立和合併 CSR。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: aa631f4c505200c2c8abc67d4e22ffbab23e015c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789022"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>在 Key Vault 中建立和合併 CSR

Azure Key Vault 支援儲存任何憑證授權單位 (CA) 發行的數位憑證。 其支援以私人/公開金鑰組建立憑證簽署要求 (CSR)。 CSR 可以由任何 CA (內部企業 CA 或外部公用 CA) 簽署。 CSR 是您傳送給 CA 以要求數位憑證的訊息。

如需更多有關憑證的一般資訊，請參閱 [Azure Key Vault 憑證](./about-certificates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>在 Key Vault 中新增合作 CA 所簽發的憑證

Key Vault 與下列憑證授權單位合作，以簡化建立憑證的程序。

|提供者|憑證類型|組態設定  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault 透過 DigiCert 提供 OV 或 EV SSL 憑證| [整合指南](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault 透過 GlobalSign 提供 OV 或 EV SSL 憑證| [整合指南](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>在 Key Vault 中新增非合作 CA 所簽發的憑證

請遵循下列步驟，針對從未與 Key Vault 合作的 CA 新增憑證。 (例如，GoDaddy 不是受信任的 Key Vault CA。)

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 移至您想要新增憑證的金鑰保存庫。
1. 在屬性頁面上，選取 [憑證]。
1. 選取 [產生/匯入] 索引標籤。
1. 在 [建立憑證] 畫面中，選擇以下值：
    - **憑證建立方法**：產生。
    - **憑證名稱**：ContosoManualCSRCertificate。
    - **憑證授權單位 (CA) 的類型**：非整合式 CA 所發行的憑證。
    - **主旨**：`"CN=www.contosoHRApp.com"`。
     > [!NOTE]
     > 如果您要使用值中有逗號 (，) 的相對辨別名稱 (RDN)，以雙引號括住包含特殊字元的值。 
     >
     > **主體** 的範例輸入：`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > 在此範例中，RDN `OU` 包含名稱中含有逗號的值。 `OU` 產生的輸出為 **Docs, Contoso**。
1. 視需要選取其他值，然後選取 [建立]，將憑證新增至 [憑證] 清單中。

    ![憑證屬性螢幕擷取畫面](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. 在 **憑證** 清單中，選取新的憑證。 憑證的目前狀態會是 **已停用**，因為 CA 尚未簽署該憑證。
1. 在 [憑證作業] 索引標籤選取 [下載 CSR]。

   ![醒目提示 [下載 CSR] 按鈕的螢幕擷取畫面。](../media/certificates/create-csr-merge-csr/download-csr.png)

1. 讓 CA 簽署 CSR (.csr)。
1. 簽署要求之後，請選取 [憑證作業] 索引標籤上的 [合併簽署的要求]，將簽署的憑證新增至 Key Vault。

憑證要求現已成功合併。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 建立憑證原則。 因為並未與此案例中選擇的 CA 合作，**IssuerName** 設定為 **不明**，而且 Key Vault 不會註冊或更新憑證。

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > 如果您要使用值中有逗號 (，) 的相對辨別名稱 (RDN)，請使用單引號括住完整值或值集，並以雙引號括住包含特殊字元的值。 
     >
     >**SubjectName** 的範例輸入：`$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`。 在此範例中，`OU` 值會顯示為 **Docs, Contoso**。 此格式適用於所有包含逗號的值。
     > 
     > 在此範例中，RDN `OU` 包含名稱中含有逗號的值。 `OU` 產生的輸出為 **Docs, Contoso**。

1. 建立 CSR。

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. 讓 CA 簽署 CSR。 `$csr.CertificateSigningRequest` 是憑證的基底編碼 CSR。 您可以將此 Blob 傾印到簽發者的憑證要求網站。 此步驟會因 CA 而異。 查閱如何執行此步驟的 CA 指導方針。 您也可以使用 certreq 或 openssl 之類的工具來取得已簽署的 CSR，並完成憑證產生流程。

1. 在 Key Vault 中合併已簽署的要求。 簽署憑證要求之後，您可以將其與 Azure Key Vault 中建立的初始私人/公開金鑰組合併。

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

憑證要求現已成功合併。

---

## <a name="add-more-information-to-the-csr"></a>在 CSR 中新增更多資訊

如果要在建立 CSR 時新增更多資訊，請在 **SubjectName** 中定義。 建議您新增下列資訊：
- 國家/地區
- 城市/地區
- 州/省
- 組織
- 組織單位

範例

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> 如果您要求具有其他資訊的網域驗證 (DV) 憑證，CA 可能會在無法驗證要求中的所有資訊時拒絕要求。 如果您要求組織驗證 (OV) 憑證，其他資訊可能更適合。

## <a name="faqs"></a>常見問題集

- 如何監視或管理我的 CSR？

     請參閱[監視和管理憑證建立](./create-certificate-scenarios.md)。

- 如果看到 **錯誤類型「在指定的 x.509 憑證內容中，終端實體憑證的公開金鑰不符合所指定私密金鑰的公開部分。請檢查憑證是否有效」** 該怎麼辦？

     如果您並未將已簽署的 CSR 與起始的相同 CSR 要求合併，就會發生此錯誤。 您所建立的每個新 CSR 都有一個私密金鑰，當您合併已簽署的要求時，該金鑰必須相符。

- 合併 CSR 後，整個鏈結都會合併嗎？

     是的，系統會合併整個鏈結，前提是使用者已送回 .p7b 檔案以合併。

- 如果在 Azure 入口網站中發出的憑證處於已停用狀態，該怎麼辦？

     檢視 **憑證作業** 索引標籤，以檢閱該憑證的錯誤訊息。

- 如果我看到 **錯誤類型「提供的主體名稱不是有效的 X500 名稱」** ，該怎麼辦？

     如果 **SubjectName** 包含任何特殊字元，則可能會發生此錯誤。 請參閱 Azure 入口網站和 PowerShell 指示中的附註。

---

## <a name="next-steps"></a>後續步驟

- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
- [Azure Key Vault REST API 參考](/rest/api/keyvault)
- [保存庫 - 建立或更新](/rest/api/keyvault/vaults/createorupdate)
- [保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)