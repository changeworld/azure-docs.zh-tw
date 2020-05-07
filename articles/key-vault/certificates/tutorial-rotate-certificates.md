---
title: 教學課程 - 在 Key Vault 中更新憑證的自動輪替頻率 | Microsoft Docs
description: 本教學課程示範如何使用 Azure 入口網站在 Azure Key Vault 中更新憑證的自動輪替頻率
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107555"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>教學課程：在 Key Vault 中設定憑證的自動輪替

Azure Key Vault 可讓您輕鬆地佈建、管理及部署數位憑證。 這些數位憑證可以是由憑證授權單位單位或自我簽署憑證所簽署的公用和私人 SSL/TLS 憑證。 Key Vault 也可以透過與憑證授權單位合作來要求和更新憑證，進而為憑證生命週期管理提供強大的解決方案。 在本教學課程中，您將更新憑證的屬性：有效期間、自動輪替頻率、CA。 如需 Key Vault 的詳細資訊，您可以檢閱[概觀](../general/overview.md)。

本教學課程說明如何：

> [!div class="checklist"]
> * 使用 Azure 入口網站管理憑證
> * 新增憑證授權單位單位提供者帳戶
> * 更新憑證有效期間
> * 更新憑證的自動輪替頻率
> * 使用 Azure Powershell 更新憑證的屬性


在開始之前，請先閱讀 [Key Vault 基本概念](../general/basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vault"></a>建立保存庫

建立或選取現有金鑰保存庫以執行作業。 [(建立金鑰保存庫的步驟)。](../quick-create-portal.md) 在此範例中，保存庫名稱為 **Example-Vault**。 

![Key Vault 建立完成後的輸出](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>在 Key Vault 中建立憑證

在保存庫中建立或匯入憑證。 [(在金鑰保存庫中建立憑證的步驟)。](../quick-create-portal.md) 在此情況下，我們會處理名為 **ExampleCertificate** 的憑證。

> [!NOTE]
> 在 Azure Key Vault 中，憑證的生命週期屬性可以在建立憑證時以及在建立憑證之後進行更新。 
## <a name="updating-certificates-life-cycle-attributes"></a>更新憑證的生命週期屬性

在金鑰保存庫中建立的憑證可以是 
- 自我簽署憑證
- 使用與 Key Vault 合作的憑證授權單位所建立的憑證
- 使用未與 Key Vault 合作的憑證授權單位所建立的憑證

下列憑證授權單位目前是與 Key Vault 合作的提供者：
- DigiCert - Key Vault 透過 DigiCert 提供 OV TLS/SSL 憑證。
- GlobalSign - Key Vault 透過 GlobalSign 提供 OV TLS/SSL 憑證。

Azure Key Vault 會透過與憑證授權單位的合作關係自動輪替憑證。 透過該建立的合作關係，Key Vault 會自動要求和更新憑證。 因此，**自動輪替功能不適用於使用未與 Key Vault 合作的 CA 所建立的憑證。** 

> [!NOTE]
> CA 提供者的帳戶管理員會建立 Key Vault 用來透過 Key Vault 建立、更新和使用 TLS/SSL 憑證的認證。
![憑證授權單位](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>在建立憑證時更新憑證的生命週期屬性

1. 在 Key Vault 屬性頁面上，選取 [憑證]  。
2. 按一下 [產生/匯入]  。
3. 在 [建立憑證]  畫面上，更新下列值：
    

    - **有效期間**：輸入此值 (以月份為單位)。 建立短期憑證是建議的安全性做法。 新建憑證的有效期間值預設為 12 個月。
    - **存留期動作類型**：選取憑證的自動更新和警示動作。 根據選取項目，更新「存留期百分比」或「到期之前的天數」。 根據預設，憑證的自動更新會設定在其存留期的 80%。<br> 從下拉式功能表中，選取選項：

    |  在指定時間自動更新| 在指定時間傳送電子郵件給所有連絡人 |
    |-----------|------|
    |選取此選項將會開啟自動輪替 | 選取此選項將不會自動輪替，只會對連絡人發出警示|
        


4. 按一下 [建立]  。

![憑證生命週期](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>更新預存憑證的生命週期屬性

1. 選取金鑰保存庫。
2. 在 Key Vault 屬性頁面上，選取 [憑證]  。
3. 選取您想要更新的憑證。 在此情況下，我們會處理名為 **ExampleCertificate** 的憑證。
4. 從頂端功能表列中選取 [發行原則]  。

![憑證屬性](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. 在 [發行原則]  畫面上，更新下列值：
- **有效期間**：更新此值 (以月份為單位)
- **存留期動作類型**：選取憑證的自動更新和警示動作。 根據選取項目，更新「存留期百分比」或「到期之前的天數」。 

![憑證屬性](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. 按一下 [ **儲存**]。

> [!IMPORTANT]
> 變更憑證的存留期動作類型會立即記錄現有憑證的修改內容。


### <a name="updating-certificates-attributes-using-powershell"></a>使用 PowerShell 更新憑證的屬性

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 若要修改憑證清單的更新原則，請輸入包含 VaultName,CertName 的 File.csv <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
在[這裡](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)深入了解參數

## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
如果不再需要，請刪除資源群組，這會刪除 Key Vault 和相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]  。
3. 在 [輸入資源群組名稱:]  方塊中輸入資源群組的名稱，然後選取 [刪除]  。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已更新憑證的生命週期。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

深入了解如何[在 Azure Key Vault 中管理憑證建立](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- 檢閱 [Key Vault 概觀](../general/overview.md)