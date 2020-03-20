---
title: 客戶管理的金鑰磁片加密，適用于 Azure HDInsight
description: 本文說明如何從 Azure Key Vault 使用您自己的加密金鑰，以加密在 Azure HDInsight 叢集的受控磁片上儲存的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: fd5308574e84ab6d2e30b9352254683b2d1d6fdd
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78403571"
---
# <a name="customer-managed-key-disk-encryption"></a>客戶管理金鑰磁碟加密

Azure HDInsight 支援客戶管理的金鑰加密，適用于受控磁片上的資料，以及連接至 HDInsight 叢集虛擬機器的資源磁片。 這項功能可讓您使用 Azure Key Vault 來管理在 HDInsight 叢集上保護待用資料的加密金鑰。 

HDInsight 中的所有受控磁碟都會使用 Azure 儲存體服務加密 (SSE) 來加以保護。 根據預設，這些磁碟上的資料會使用 Microsoft 所管理的金鑰來加密。 如果您為 HDInsight 啟用客戶管理的金鑰，您可以提供 HDInsight 的加密金鑰，以使用 Azure Key Vault 管理這些金鑰。

本檔不會處理儲存在 Azure 儲存體帳戶中的資料。 如需 Azure 儲存體加密的詳細資訊，請參閱[靜態資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。 您的叢集可能會有一或多個附加的 Azure 儲存體帳戶，其中的加密金鑰也可以是 Microsoft 管理或由客戶管理，但加密服務不同。

## <a name="introduction"></a>簡介

客戶管理的金鑰加密是在叢集建立期間處理的單一步驟程式，不需額外費用。 您只需要使用 Azure Key Vault 將 HDInsight 註冊為受控識別，並在建立叢集時新增加密金鑰即可。

叢集的每個節點上的資源磁片和受控磁片都會以對稱資料加密金鑰（DEK）進行加密。 DEK 會使用金鑰保存庫中的金鑰加密金鑰 (KEK) 來加以保護。 加密和解密程序完全由 Azure HDInsight 來處理。

如果已在儲存磁片加密金鑰的金鑰保存庫上啟用金鑰保存庫防火牆，則必須將部署叢集所在區域的 HDInsight 地區資源提供者 IP 位址新增至金鑰保存庫防火牆設定。 這是必要的，因為 HDInsight 不是受信任的 Azure 金鑰保存庫服務。

您可以使用 Azure 入口網站或 Azure CLI，在金鑰保存庫中安全地輪替金鑰。 當金鑰旋轉時，HDInsight 叢集會在幾分鐘內開始使用新的金鑰。 啟用虛[刪除](../key-vault/key-vault-ovw-soft-delete.md)金鑰保護功能，以防範勒索軟體案例和意外刪除。 不支援不含此保護功能的金鑰保存庫。

|叢集類型 |OS 磁片（受控磁片） |資料磁片（受控磁片） |暫存資料磁片（本機 SSD） |
|---|---|---|---|
|具有加速寫入的 Kafka、HBase|[SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE 加密 + 選擇性 CMK 加密|選擇性的 CMK 加密|
|所有其他叢集（Spark、互動式、Hadoop、不含加速寫入的 HBase）|SSE 加密|N/A|選擇性的 CMK 加密|

## <a name="get-started-with-customer-managed-keys"></a>開始使用客戶管理的金鑰

若要建立已啟用客戶管理的 HDInsight 叢集，請執行下列步驟：

1. 建立適用于 Azure 資源的受控識別
1. 建立 Azure Key Vault
1. 建立金鑰
1. 建立存取原則
1. 建立已啟用客戶管理金鑰的 HDInsight 叢集
1. 輪替加密金鑰

## <a name="create-managed-identities-for-azure-resources"></a>建立適用于 Azure 資源的受控識別

建立使用者指派的受控識別，以向 Key Vault 進行驗證。

如需特定步驟，請參閱[建立使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 如需如何在 Azure HDInsight 中使用受控識別的詳細資訊，請參閱[Azure HDInsight 中的受控](hdinsight-managed-identities.md)識別。 將受控識別資源識別碼新增至 Key Vault 存取原則時，請務必儲存起來。

## <a name="create-azure-key-vault"></a>建立 Azure Key Vault

建立金鑰保存庫。 如需特定步驟，請參閱[建立 Azure Key Vault](../key-vault/quick-create-portal.md) 。

HDInsight 僅支援 Azure Key Vault。 如果您有自己的金鑰保存庫，則可以將自己的金鑰匯入 Azure Key Vault 中。 請記住，key vault 必須已啟用虛**刪除**。 如需如何匯入現有金鑰的詳細資訊，請瀏覽[關於金鑰、祕密和憑證](../key-vault/about-keys-secrets-and-certificates.md)。

## <a name="create-key"></a>建立金鑰

1. 從您的新金鑰保存庫，流覽至 [**設定**] > [**金鑰**] >  **+ [產生/匯入**]。

    ![在 Azure Key Vault 中產生新的金鑰](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中產生新的金鑰")

1. 提供名稱，然後選取 [**建立**]。 維護預設的**RSA** **金鑰類型**。

    ![產生索引鍵名稱](./media/disk-encryption/create-key.png "產生金鑰名稱")

1. 當您返回 [**金鑰**] 頁面時，請選取您建立的金鑰。

    ![金鑰保存庫金鑰清單](./media/disk-encryption/key-vault-key-list.png)

1. 選取版本以開啟 [**金鑰版本**] 頁面。 當您使用自己的 HDInsight 叢集加密金鑰時，您需要提供金鑰 URI。 複製**金鑰識別碼**並將其儲存到某處，直到您準備好建立叢集為止。

    ![取得金鑰識別碼](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>建立存取原則

1. 從您的新金鑰保存庫，流覽至 [**設定**] > [**存取原則**] > [ **+ 新增存取原則**]。

    ![建立新的 Azure Key Vault 存取原則](./media/disk-encryption/key-vault-access-policy.png)

1. 在 [**新增存取原則**] 頁面上，提供下列資訊：

    |屬性 |描述|
    |---|---|
    |金鑰許可權|選取 [**取得**]、[解除包裝**金鑰**] 和 [**包裝金鑰**]。|
    |秘密許可權|選取 [**取得**]、[**設定**] 和 [**刪除**]。|
    |選取主體|選取您稍早建立的使用者指派受控識別。|

    ![為 Azure Key Vault 存取原則設定 [選取主體]](./media/disk-encryption/azure-portal-add-access-policy.png)

1. 選取 [新增]。

1. 選取 [儲存]。

    ![儲存 Azure Key Vault 存取原則](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>使用客戶管理的金鑰磁片加密來建立叢集

您現在可以開始建立新的 HDInsight 叢集。 客戶管理的金鑰只能在叢集建立期間套用至新叢集。 無法從客戶管理的金鑰叢集移除加密，而且無法將客戶管理的金鑰新增至現有的叢集。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

在叢集建立期間，請提供完整**金鑰識別碼**，包括金鑰版本。 例如，`https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`。 您也需要將受控識別指派給叢集，並提供金鑰 URI。

![建立新叢集](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下列範例示範如何使用 Azure CLI 建立新的 Apache Spark 叢集，並啟用磁片加密。 如需詳細資訊，請參閱[Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>輪替加密金鑰

在某些情況下，您可能會想要在建立 HDInsight 叢集之後變更其所使用的加密金鑰。 這可以透過入口網站輕鬆地進行。 針對此作業，叢集必須能夠存取目前的金鑰和預定的新金鑰，否則輪替金鑰作業將會失敗。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要輪替金鑰，您需要基底金鑰保存庫 URI。 完成之後，請移至入口網站中的 [HDInsight 叢集內容] 區段，然後按一下 [**磁片加密金鑰 URL**] 底下的 [**變更金鑰**]。 輸入新的金鑰 url 並提交以旋轉金鑰。

![輪替磁片加密金鑰](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

下列範例顯示如何輪替現有 HDInsight 叢集的磁片加密金鑰。 如需詳細資訊，請參閱[Azure CLI az hdinsight 旋轉-磁片加密金鑰](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>客戶管理的金鑰加密常見問題

**HDInsight 叢集如何存取我的金鑰保存庫？**

HDInsight 會使用與 HDInsight 叢集相關聯的受控識別來存取您的 Azure Key Vault 實例。 您可以在叢集建立之前或建立期間，建立這個受控識別。 您也需要對受控識別授與權限，使其能夠存取金鑰儲存所在的金鑰保存庫。

**這項功能是否適用于 HDInsight 上的所有叢集？**

客戶管理的金鑰加密適用于 Spark 2.1 和2.2 以外的所有叢集類型。

**我可以使用多個金鑰來加密不同的磁片或資料夾嗎？**

否，所有受控磁片和資源磁片都會以相同的金鑰加密。

**如果叢集失去對金鑰保存庫或金鑰的存取權，會發生什麼事？**

如果叢集失去金鑰的存取權，Apache Ambari 入口網站中將會顯示警告。 在此狀態下，**變更金鑰**作業將會失敗。 一旦還原金鑰存取權，Ambari 警告就會消失，而且可以順利執行金鑰輪替之類的作業。

![金鑰存取權 Ambari 警示](./media/disk-encryption/ambari-alert.png)

**如果金鑰已刪除，要如何復原叢集？**

因為只支援「虛刪除」啟用的金鑰，所以如果金鑰保存庫中的金鑰已復原，叢集應該會重新取得金鑰的存取權。 若要復原 Azure Key Vault 金鑰，請參閱 [復原-AzKeyVaultKeyRemova](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)l或[az-keyvault-Key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。

**加密哪些磁片類型？OS 磁片/資源磁片是否也會加密？**

資源磁片和資料/受控磁片會進行加密。 作業系統磁片不會加密。

**如果叢集已相應增加，新節點是否會順暢地支援客戶管理的金鑰？**

可以。 叢集在擴大期間需要存取金鑰保存庫中的金鑰。 相同的金鑰可用來加密叢集中的受控磁片和資源磁片。

**客戶管理的金鑰是否可用於我的位置？**

HDInsight 客戶管理的金鑰適用于所有公用雲端和國家雲端。

## <a name="next-steps"></a>後續步驟

* 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault](../key-vault/key-vault-overview.md)。
* [Azure HDInsight 中的企業安全性總覽](./domain-joined/hdinsight-security-overview.md)。
