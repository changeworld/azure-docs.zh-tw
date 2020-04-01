---
title: 從 Azure HDInsight 的客戶管理金鑰磁碟加密
description: 本文介紹如何使用 Azure 密鑰保管庫中自己的加密密鑰來加密存儲在 Azure HDInsight 群集中託管磁碟上的數據。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c0521f384a333c3054397fb0ec7c2ab907e54f67
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411757"
---
# <a name="customer-managed-key-disk-encryption"></a>客戶管理金鑰磁碟加密

Azure HDInsight 支援客戶管理的關鍵加密,用於託管磁碟和連接到 HDInsight 叢集虛擬機器的資源磁碟上的數據。 此功能允許您使用 Azure 密鑰保管庫來管理保護 HDInsight 群集上靜態數據的加密密鑰。

HDInsight 中的所有受控磁碟都會使用 Azure 儲存體服務加密 (SSE) 來加以保護。 根據預設，這些磁碟上的資料會使用 Microsoft 所管理的金鑰來加密。 如果為 HDInsight 啟用客戶管理的密鑰,則可以為 HDInsight 提供加密金鑰,以便使用 Azure 金鑰保管庫使用這些密鑰並管理這些密鑰。

本文檔不解決存儲在 Azure 儲存帳戶中的數據。 有關 Azure 儲存加密的詳細資訊,請參考[靜態資料的 Azure 儲存加密](../storage/common/storage-service-encryption.md)。 群集可能具有一個或多個附加的 Azure 儲存帳戶,其中加密密鑰也可以由 Microsoft 管理或客戶管理,但加密服務不同。

## <a name="introduction"></a>簡介

客戶管理密鑰加密是在群集創建期間處理的一步過程,無需額外費用。 您只需要使用 Azure Key Vault 將 HDInsight 註冊為受控識別，並在建立叢集時新增加密金鑰即可。

叢集每個節點上的資源磁碟和託管磁碟都使用對稱數據加密密鑰 (DEK) 進行加密。 DEK 會使用金鑰保存庫中的金鑰加密金鑰 (KEK) 來加以保護。 加密和解密程序完全由 Azure HDInsight 來處理。

如果在儲存磁碟加密密鑰的密鑰保管庫上啟用密鑰保管庫防火牆,則必須將部署群集的區域的 HDInsight 區域資源提供程式 IP 位址添加到密鑰保管庫防火牆配置中。 這是必需的,因為 HDInsight 不是受信任的 Azure 密鑰保管庫服務。

您可以使用 Azure 入口網站或 Azure CLI，在金鑰保存庫中安全地輪替金鑰。 當金鑰旋轉時,HDInsight 群集將在幾分鐘內開始使用新密鑰。 啟用[軟刪除](../key-vault/key-vault-ovw-soft-delete.md)密鑰保護功能,以防止勒索軟體方案和意外刪除。 不支援沒有此保護功能的密鑰保管庫。

|叢集類型 |OS 磁碟 (託管磁碟) |資料磁碟 (託管磁碟) |暫存資料磁碟(本地 SSD) |
|---|---|---|---|
|卡夫卡,HBase,加速寫入|[SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE 加密 + 選擇 CMK 加密|選擇的 CMK 加密|
|所有其他群集(火花、互動式、Hadoop、HBase,無加速寫入)|SSE 加密|N/A|選擇的 CMK 加密|

## <a name="get-started-with-customer-managed-keys"></a>開始使用客戶管理的金鑰

要建立啟用客戶管理的啟用金鑰的 HDInsight 叢集,我們將執行以下步驟:

1. 為 Azure 資源建立託管識別
1. 建立 Azure Key Vault
1. 建立金鑰
1. 建立存取原則
1. 建立啟用客戶管理金鑰的 HDInsight 叢集
1. 旋轉加密金鑰

## <a name="create-managed-identities-for-azure-resources"></a>為 Azure 資源建立託管識別

創建使用者分配的託管標識以對密鑰保管庫進行身份驗證。

有關特定步驟[,請參閱創建使用者分配的託管標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 有關託管識別在 Azure HDInsight 中的工作方式的詳細資訊,請參閱[Azure HDInsight 中的託管標識](hdinsight-managed-identities.md)。 將受控識別資源識別碼新增至 Key Vault 存取原則時，請務必儲存起來。

## <a name="create-azure-key-vault"></a>建立 Azure Key Vault

建立金鑰保存庫。 有關特定步驟,請參閱[建立 Azure 金鑰保管庫](../key-vault/quick-create-portal.md)。

HDInsight 僅支援 Azure Key Vault。 如果您有自己的金鑰保存庫，則可以將自己的金鑰匯入 Azure Key Vault 中。 請記住,金鑰保存此函式庫必須開啟 **。** 如需如何匯入現有金鑰的詳細資訊，請瀏覽[關於金鑰、祕密和憑證](../key-vault/about-keys-secrets-and-certificates.md)。

## <a name="create-key"></a>建立金鑰

1. 從新金鑰保管庫,導覽到**設定** > **鍵** > **+ 產生/匯入**。

    ![在 Azure Key Vault 中產生新的金鑰](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中產生新的金鑰")

1. 提供名稱,然後選擇 **"創建**"。 維護**RSA**的預設**金鑰類型**。

    ![產生金鑰名稱](./media/disk-encryption/create-key.png "產生金鑰名稱")

1. 傳回**為 「金鑰」** 頁時,請選擇您建立的金鑰。

    ![金鑰保存金鑰清單](./media/disk-encryption/key-vault-key-list.png)

1. 選擇要開啟 **「金鑰版本」 頁的版本**。 當您使用自己的金鑰進行 HDInsight 群集加密時,您需要提供金鑰 URI。 複製**金鑰識別碼**並將其儲存到某處，直到您準備好建立叢集為止。

    ![取得金鑰識別碼](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>建立存取原則

1. 從新的金鑰保管庫,導覽到**設定** > **存取原則** > **和新增存取原則**。

    ![建立新的 Azure Key Vault 存取原則](./media/disk-encryption/key-vault-access-policy.png)

1. 在 **'新增存取策略'** 頁中,提供以下資訊:

    |屬性 |描述|
    |---|---|
    |金鑰權限|選擇 **,****取消包裝鍵**與**包裝鍵**。|
    |秘密權限|選擇 **,****設定**與**刪除**。|
    |選擇主體|選擇您之前創建的使用者分配的託管標識。|

    ![為 Azure Key Vault 存取原則設定 [選取主體]](./media/disk-encryption/azure-portal-add-access-policy.png)

1. 選取 [新增]  。

1. 選取 [儲存]  。

    ![儲存 Azure 金鑰保存的權限存取原則](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>使用客戶管理的關鍵磁碟加密建立叢集

您現在可以開始建立新的 HDInsight 叢集。 客戶管理的金鑰只能在群集創建期間應用於新群集。 無法從客戶管理的關鍵群集中刪除加密,並且客戶管理的密鑰無法添加到現有群集。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

在群集創建期間,提供完整的**密鑰標識符**,包括密鑰版本。 例如： `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 。 您也需要將受控識別指派給叢集，並提供金鑰 URI。

![建立新叢集](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的範例展示如何使用 Azure CLI 創建啟用磁碟加密的新 Apache Spark 群集。 有關詳細資訊,請參閱[Azure CLI az hdinsight 創建](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>旋轉加密金鑰

在某些情況下,您可能希望更改 HDInsight 群集在創建後使用的加密密鑰。 這可以通過門戶輕鬆。 對於此操作,群集必須同時訪問當前密鑰和預期的新密鑰,否則旋轉鍵操作將失敗。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

要旋轉金鑰,您需要基密鑰保管庫URI。 完成此動作後,轉到門戶中的 HDInsight 叢集屬性部分,然後按下**磁碟加密金鑰網址**下的**變更金鑰**。 輸入新的密鑰 URL 並提交以旋轉金鑰。

![旋轉磁碟加密金鑰](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下面的範例展示如何旋轉現有HDInsight群集的磁碟加密密鑰。 有關詳細資訊,請參閱[Azure CLI az hdinsight 旋轉磁碟加密金鑰](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

要使用資源管理員樣本使用客戶託管金鑰,請使用以下變更更新樣本:

1. 在**azuredeploy.json**檔中,向資源「物件添加以下屬性:

    ```json
       "diskEncryptionProperties":
         {
                 "vaultUri": "[parameters('diskEncryptionVaultUri')]",
                  "keyName": "[parameters('diskEncryptionKeyName')]",
                  "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
                   "msiResourceId": "[parameters('diskEncryptionMsiResourceId')]"
         }

1. In the **azuredeploy.parameters.json** file, add the following parameters. You can get the values of these parameters from the Key Vault URI and the managed Identity. For example, if you have the following URI and identity values,
    * Sample key vault URI: https://<KeyVault_Name>.vault.azure.net/keys/clusterkey/<Cluster_Key_Value>
    * Sample user-assigned managed identity: "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>

    The parameters in the **azuredeploy.parameters.json** file are:

    ```json
   "diskEncryptionVaultUri": {
            "value": "https://<KeyVault_Name>.vault.azure.net"
        },
        "diskEncryptionKeyName": {
            "value": "clusterkey"
        },
        "diskEncryptionKeyVersion": {
            "value": "<Cluster_Key_Value>"
        },
        "diskEncryptionMsiResourceId": {
            "value": "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>"
        }
    ```

## <a name="faq-for-customer-managed-key-encryption"></a>客戶管理金鑰加密常見問題解答

**HDInsight 群集如何訪問我的密鑰保管庫?**

HDInsight 使用與 HDInsight 群集關聯的託管標識訪問 Azure 密鑰保管庫實例。 您可以在叢集建立之前或建立期間，建立這個受控識別。 您也需要對受控識別授與權限，使其能夠存取金鑰儲存所在的金鑰保存庫。

**此功能是否可用於 HDInsight 上的所有群集?**

除 Spark 2.1 和 2.2 外,所有群集類型都可用於客戶管理密鑰加密。

**我可以使用多個金鑰來加密不同的磁碟或資料夾嗎?**

否,所有託管磁碟和資源磁碟都使用相同的密鑰加密。

**如果群集無法訪問金鑰保管庫或密鑰,會發生什麼情況?**

如果群集無法存取金鑰,警告將顯示在 Apache Ambari 入口中。 在此狀態下,「**更改金鑰」** 操作將失敗。 復原金鑰存取後,Ambari 警告將消失,並且可以成功執行金鑰輪換等操作。

![金鑰存取安巴里警報](./media/disk-encryption/ambari-alert.png)

**如果金鑰已刪除，要如何復原叢集？**

由於僅支援"軟刪除"啟用的密鑰,因此,如果在密鑰保管庫中恢復密鑰,群集應重新獲得對密鑰的訪問許可權。 要回復 Azure 金鑰保存庫金鑰,請參閱[復原-AzKeyVaultKey刪除](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)或[az 鍵保管庫金鑰恢復](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。

**哪些磁碟類型已加密?OS 磁碟/資源磁碟也加密嗎?**

資源磁碟和數據/託管磁碟已加密。 操作系統磁碟未加密。

**如果擴展了群集,新節點是否無縫地支持客戶管理的密鑰?**

是。 叢集在相應增加期間需要存取金鑰保存庫中的金鑰。 同一密鑰用於加密群集中的託管磁碟和資源磁碟。

**我的位置有客戶管理的密鑰嗎?**

HDInsight 客戶管理的關鍵在所有公共雲和國家雲中都有可用。

## <a name="next-steps"></a>後續步驟

* 有關 Azure 金鑰保存庫的詳細資訊,請參閱[什麼是 Azure 金鑰保管庫](../key-vault/key-vault-overview.md)。
* [Azure HDInsight 中的企業安全性概述](./domain-joined/hdinsight-security-overview.md)。
