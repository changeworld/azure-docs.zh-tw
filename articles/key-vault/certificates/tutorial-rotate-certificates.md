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
ms.openlocfilehash: 2d981e902f829eb0fa8283b6a38ae376a780bcc9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289743"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>教學課程：在 Key Vault 中設定憑證的自動輪替

您可以利用 Azure Key Vault 輕鬆地佈建、管理及部署數位憑證。 憑證可以是由憑證授權單位 (CA) 簽署的公用和私人安全通訊端層 (SSL)/傳輸層安全性 (TLS) 憑證，或自我簽署憑證。 Key Vault 也可以透過與 CA 合作來要求和更新憑證，進而為憑證生命週期管理提供強大的解決方案。 在本教學課程中，您將更新憑證的有效期間、自動輪替頻率、CA 屬性。

本教學課程說明如何：

> [!div class="checklist"]
> * 使用 Azure 入口網站管理憑證。
> * 新增 CA 提供者帳戶。
> * 更新憑證的有效期間。
> * 更新憑證的自動輪替頻率。
> * 使用 Azure PowerShell 更新憑證的屬性。

在開始之前，請先閱讀 [Key Vault 基本概念](../general/basic-concepts.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vault"></a>建立保存庫

建立金鑰保存庫，或選取現有的保存庫以執行作業 (請參閱[建立金鑰保存庫的步驟](../secrets/quick-create-portal.md))。 在此範例中，金鑰保存庫名稱為 **Example-Vault**。

![金鑰保存庫建立完成後的輸出](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>在 Key Vault 中建立憑證

建立憑證或將憑證匯入金鑰保存庫中 (請參閱[在 Key Vault 中建立憑證的步驟](../secrets/quick-create-portal.md))。 在此案例中，您會處理名為 **ExampleCertificate** 的憑證。

## <a name="update-certificate-lifecycle-attributes"></a>更新憑證生命週期屬性

在 Azure Key Vault 中，您可以在建立憑證之前和之後更新憑證的生命週期屬性。

在 Key Vault 中建立的憑證可以是：

- 自我簽署憑證。
- 使用與 Key Vault 合作的 CA 建立的憑證。
- 未與 Key Vault 合作的 CA 所建立的憑證。

下列 CA 是目前與 Key Vault 合作的提供者：

- DigiCert：Key Vault 提供 OV TLS/SSL 憑證。
- GlobalSign：Key Vault 提供 OV TLS/SSL 憑證。

Key Vault 會透過與 CA 已有的合作關係自動輪替憑證。 因為 Key Vault 會透過合作關係自動要求和更新憑證，因此，自動輪替功能不適用於未與 Key Vault 合作的 CA 所建立的憑證。

> [!NOTE]
> CA 提供者的帳戶管理員會建立供 Key Vault 用來建立、更新和使用 TLS/SSL 憑證的認證。
![憑證授權單位](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>在建立時更新憑證生命週期屬性

1. 在 Key Vault 屬性頁面上，選取 [憑證]。
1. 選取 [產生/匯入]。
1. 在 [建立憑證] 畫面上，更新下列值：

   - **有效期間**：輸入此值 (以月份為單位)。 建立短期憑證是建議的安全性做法。 新建憑證的有效期間值預設為 12 個月。
   - **存留期動作類型**：選取憑證的自動更新和警示動作，然後更新 [存留期百分比] 或 [到期之前的天數]。 根據預設，憑證的自動更新會設定在其存留期的 80%。 從下拉式功能表中，選取下列其中一個選項。

        |  在指定時間自動更新| 在指定時間傳送電子郵件給所有連絡人 |
        |-----------|------|
        |選取此選項將會 *開啟* 自動輪替。 | 選取此選項將 *不會* 進行自動輪替，而只會對連絡人發出警示。|

1. 選取 [建立]。

![憑證生命週期](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>更新預存憑證的生命週期屬性

1. 選取金鑰保存庫。
1. 在 Key Vault 屬性頁面上，選取 [憑證]。
1. 選取您要更新的憑證。 在此案例中，您會處理名為 **ExampleCertificate** 的憑證。
1. 從頂端功能表列中選取 [發行原則]。

   ![醒目提示 [發行原則] 按鈕的螢幕擷取畫面。](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. 在 [發行原則] 畫面上，更新下列值：

   - **有效期間**：更新此值 (以月為單位)。
   - **存留期動作類型**：選取憑證的自動更新和警示動作，然後更新 [存留期百分比] 或 [到期之前的天數]。

   ![憑證屬性](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. 選取 [儲存]。

> [!IMPORTANT]
> 變更憑證的存留期動作類型會立即記錄現有憑證的修改內容。


### <a name="update-certificate-attributes-by-using-powershell"></a>使用 PowerShell 更新憑證屬性

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 若要修改憑證清單的更新原則，請輸入包含 `VaultName,CertName` 的 `File.csv`，如下列範例所示：
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
若要深入了解參數，請參閱 [az keyvault certificate](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)。

## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 教學課程以本教學課程作為建置基礎。 如果您想要使用這些教學課程，您可以保留這些現有的資源。
如果不需再使用，請刪除資源群組；這將會連帶刪除金鑰保存庫和相關資源。

若要使用入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 如果搜尋結果中顯示您在本快速入門中使用的資源群組，請加以選取。
1. 選取 [刪除資源群組]。
1. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已更新憑證的生命週期屬性。 若要深入了解 Key Vault 以及如何將其與應用程式整合，請繼續閱讀下列文章：

- 深入了解如何[在 Azure Key Vault 中管理憑證建立](./create-certificate-scenarios.md)。
- 檢閱 [Key Vault 概觀](../general/overview.md)。