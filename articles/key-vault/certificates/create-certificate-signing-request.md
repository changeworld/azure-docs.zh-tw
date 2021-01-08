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
ms.openlocfilehash: 42f649f9dd206b34f0fac8513ba742febed2dbcb
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724624"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>在 Key Vault 中建立和合併 CSR

Azure Key Vault 支援將您所選任何憑證授權單位發行的數位憑證儲存在金鑰保存庫中。 其支援建立具有私人/公開金鑰組的憑證簽署要求，這可由任何任何指定的憑證授權單位簽署。 其可以是內部企業 CA 或外部公用 CA。 憑證簽署要求 (也就是 CSR 或認證要求) 是由使用者傳送到憑證授權單位 (CA) 的訊息，目的是要求發行憑證。

如需更多有關憑證的一般資訊，請參閱 [Azure Key Vault 憑證](./about-certificates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>在 Key Vault 中新增合作 CA 所簽發的憑證
Key Vault 與下列兩個憑證授權單位合作，以簡化建立憑證的程序。 

|提供者|憑證類型|組態設定  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault 透過 DigiCert 提供 OV 或 EV SSL 憑證| [整合指南](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault 透過 GlobalSign 提供 OV 或 EV SSL 憑證| [整合指南](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>在 Key Vault 中新增非合作 CA 所簽發的憑證

下列步驟將協助您從非 Key Vault 合作憑證授權單位中建立憑證 (例如，GoDaddy 不是受信任的金鑰保存庫 CA) 



# <a name="portal"></a>[入口網站](#tab/azure-portal)

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

   ![醒目提示 [下載 CSR] 按鈕的螢幕擷取畫面。](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  將 .csr 檔案帶到 CA，以簽署要求。
9.  一旦 CA 簽署要求之後，請將憑證檔案帶回，以在同一個憑證作業畫面中 **合併已簽署的要求**。

憑證要求現已成功合併。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)



1. 首先，**建立憑證原則**。 Key Vault 不會代表使用者註冊或更新該簽發者的憑證，因為在此案例中選擇的 CA 未受支援，因此 IssuerName 設定為 [未知]。

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > 如果您要使用值中有逗號 (，) 的相對辨別名稱 (RDN)，請使用單引號，並以雙引號括住包含特殊字元的值。 範例： `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. 在此範例中，`OU` 值會顯示為 **Docs, Contoso**。 此格式適用於所有包含逗號的值。

2. 建立 **憑證簽署要求**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. 取得 **CA 簽署的 CSR 要求** `$csr.CertificateSigningRequest` 是憑證的 base4 編碼憑證簽署要求。 您可以採用此 Blob，並傾印到簽發者的憑證要求網站。 此步驟會因 CA 而異，最好的方式就是查閱 CA 指導方針來了解如何執行此步驟。 您也可以使用 certreq 或 openssl 之類的工具來取得已簽署的憑證要求，並完成憑證產生程序。


4. **在 Key Vault 中合併已簽署的要求**，在簽發者簽署憑證要求之後，您可以帶回已簽署的憑證，並將其與一開始在 Azure Key Vault 中建立的私人/公開金鑰組合併

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    現在已成功合併憑證要求。
---

> [!NOTE]
> 如果您的 RDN 值中有逗號，您也可以依照步驟 4 的說明以雙引號括住值，將其新增至 [主體] 欄位中。
> 「主體」的範例輸入：`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com` 在此範例中，RDN `OU` 包含名稱中含有逗號的值。 `OU` 產生的輸出為 **Docs, Contoso**。

## <a name="adding-more-information-to-csr"></a>在 CSR 中新增更多資訊

如果您想要在建立 CSR 時新增更多資訊，例如： 
    - 國家/地區：
    - 城市/地區：
    - 州/省：
    - 組織：
    - 組織單位：您可以在建立 CSR 時，透過在 subjectName 中定義該項目來新增這些所有資訊。

範例
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> 如果您要求的是具有 CSR 中那些所有詳細資料的 DV 憑證，CA 可能會拒絕要求，因為 CA 可能無法驗證要求中的這些所有資訊。 如果您要求的是 OV 憑證，則更適合在 CSR 中新增這些所有資訊。


## <a name="troubleshoot"></a>疑難排解

- 若要監視或管理憑證要求回應，請在[這裡](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)深入了解

- **錯誤類型「在指定的 x.509 憑證內容中，終端實體憑證的公開金鑰不符合所指定私密金鑰的公開部分。請檢查憑證是否有效」** 如果您並未合併 CSR 與起始的相同 CSR 要求，就會發生此錯誤。 每次建立 CSR 時，都會建立一個必須在合併已簽署要求時進行比對的私密金鑰。
    
- 合併 CSR 後，整個鏈結都會合併嗎？
    是的，系統會合併整個鏈結，前提是使用者已送回 p7b 檔案以合併。

- 如果在 Azure 入口網站中發出的憑證處於「已停用」狀態，請繼續檢視 **憑證作業**，來檢閱該憑證的錯誤訊息。

如需詳細資訊，請參閱 [Key Vault REST API 參考中的憑證作業](/rest/api/keyvault)。 如需建立權限的相關資訊，請參閱[保存庫 - 建立或更新](/rest/api/keyvault/vaults/createorupdate)和[保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。

- **錯誤類型「提供的主體名稱不是有效的 X500 名稱」** 如果您在 SubjectName 的值中包含任何「特殊字元」，就可能會發生此錯誤。 請分別參閱 Azure 入口網站和 PowerShell 指示中的附註。 

---
## <a name="next-steps"></a>後續步驟

- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
