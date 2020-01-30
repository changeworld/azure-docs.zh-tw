---
title: 在 Azure HDInsight 上攜帶您自己的 Apache Kafka 金鑰
description: 本文說明如何使用您自己在 Azure Key Vault 中的金鑰，來加密 Azure HDInsight 上的 Apache Kafka 中所儲存的資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 72fd23e4283925b91d749fef0afac4e87e93405c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841631"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>在 Azure HDInsight 上攜帶您自己的 Apache Kafka 金鑰

Azure HDInsight 可針對 Apache Kafka 支援攜帶您自己的金鑰 (BYOK)。 這項功能可讓您擁有及管理用來加密待用資料的金鑰。

HDInsight 中的所有受控磁碟都會使用 Azure 儲存體服務加密 (SSE) 來加以保護。 根據預設，這些磁碟上的資料會使用 Microsoft 所管理的金鑰來加密。 如果您啟用 BYOK，您會對 HDInsight 提供加密金鑰，以透過 Azure Key Vault 來進行使用及管理。

BYOK 加密是單一步驟的程序，您可在叢集建立期間免費處理此程序。 您只需要使用 Azure Key Vault 將 HDInsight 註冊為受控識別，並在建立叢集時新增加密金鑰即可。

所有傳送到 Kafka 叢集的訊息 (包括 Kafka 所維護的複本) 都會使用對稱資料加密金鑰 (DEK) 來加密。 DEK 會使用金鑰保存庫中的金鑰加密金鑰 (KEK) 來加以保護。 加密和解密程序完全由 Azure HDInsight 來處理。

您可以使用 Azure 入口網站或 Azure CLI，在金鑰保存庫中安全地輪替金鑰。 金鑰輪替時，HDInsight Kafka 叢集就會在幾分鐘內開始使用新的金鑰。 啟用「虛刪除」金鑰保護功能，以防範勒索軟體案例和意外刪除。 不支援不含此保護功能的金鑰保存庫。

## <a name="get-started-with-byok"></a>開始使用 BYOK

若要建立已啟用 BYOK 的 Kafka 叢集，我們會進行下列步驟：

1. 建立適用于 Azure 資源的受控識別
2. 安裝程式 Azure Key Vault 和金鑰
3. 建立已啟用 BYOK 的 HDInsight Kafka 叢集
4. 輪替加密金鑰

## <a name="create-managed-identities-for-azure-resources"></a>建立適用于 Azure 資源的受控識別

若要向 Key Vault 進行驗證，請使用[Azure 入口網站](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)、 [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)、 [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)或[Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)建立使用者指派的受控識別。 如需如何在 Azure HDInsight 中使用受控識別的詳細資訊，請參閱[Azure HDInsight 中的受控](../hdinsight-managed-identities.md)識別。 必須有 Azure Active directory 才能使用受控識別和 Kafka 的 BYOK，但不需要企業安全性套件 (ESP)。 將受控識別資源識別碼新增至 Key Vault 存取原則時，請務必儲存起來。

![在 Azure 入口網站中建立使用者指派的受控識別](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>設定 Key Vault 和金鑰

HDInsight 僅支援 Azure Key Vault。 如果您有自己的金鑰保存庫，則可以將自己的金鑰匯入 Azure Key Vault 中。 請記住，這些金鑰必須具有「虛刪除」。 「虛刪除」功能可透過 REST、.NET/C#、PowerShell 和 Azure CLI 介面取得。

1. 若要建立新的金鑰保存庫，請遵循 [Azure Key Vault](../../key-vault/quick-create-cli.md) 快速入門。 如需如何匯入現有金鑰的詳細資訊，請瀏覽[關於金鑰、祕密和憑證](../../key-vault/about-keys-secrets-and-certificates.md)。

1. 使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI 命令，在金鑰保存庫上啟用「虛刪除」。

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. 建立金鑰。

    a. 若要建立新的金鑰，從 [設定] 下方的 [金鑰] 功能表中選取 [產生/匯入]。

    ![在 Azure Key Vault 中產生新的金鑰](./media/apache-kafka-byok/kafka-create-new-key.png "在 Azure Key Vault 中產生新的金鑰")

    b. 將 [選項] 設定為 [產生]，並為金鑰提供名稱。

    ![Apache kafka 會產生金鑰名稱](./media/apache-kafka-byok/apache-kafka-create-key.png "產生金鑰名稱")

    c. 從金鑰清單中選取您建立的金鑰。

    ![Apache kafka key vault 金鑰清單](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. 當您使用自己的金鑰進行 Kafka 叢集加密時，需要提供金鑰 URI。 複製**金鑰識別碼**並將其儲存到某處，直到您準備好建立叢集為止。

    ![Apache kafka 取得金鑰識別碼](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. 將受控識別新增至金鑰保存庫存取原則。

    a. 建立新的 Azure Key Vault 存取原則。

    ![建立新的 Azure Key Vault 存取原則](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. 在 [選取主體] 底下，選擇您所建立的使用者指派受控識別。

    ![為 Azure Key Vault 存取原則設定 [選取主體]](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. 將 [金鑰權限] 設定為 [取得]、[將金鑰解除包裝] 及 [包裝金鑰]。

    ![設定 Azure Key Vault 存取 policy1 的金鑰許可權](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "設定 Azure Key Vault 存取 policy1 的金鑰許可權")

    d. 將 [祕密權限] 設定為 [取得]、[設定] 及 [刪除]。

    ![設定 Azure Key Vault 存取 policy2 的金鑰許可權](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "設定 Azure Key Vault 存取 policy2 的金鑰許可權")

    e. 選取 [儲存]。

    ![儲存 Azure Key Vault 存取原則](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>建立 HDInsight 叢集

您現在可以開始建立新的 HDInsight 叢集。 BYOK 只能在叢集建立期間套用至新的叢集。 您無法從 BYOK 叢集移除加密，也無法將 BYOK 新增到現有叢集。

![Azure 入口網站中的 Kafka 磁碟加密](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka.png)

叢集建立期間，請提供完整的金鑰 URL，包括金鑰版本。 例如： `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 。 您也需要將受控識別指派給叢集，並提供金鑰 URI。 如需完整的叢集建立詳細資料，請參閱[使用 Azure 入口網站建立 Apache Hadoop](./apache-kafka-get-started.md)叢集

## <a name="rotating-the-encryption-key"></a>輪替加密金鑰

在某些情況下，您可能會想要變更 Kafka 叢集在建立之後所使用的加密金鑰。 這可以透過入口網站輕鬆地進行。 針對此作業，叢集必須能夠存取目前的金鑰和預定的新金鑰，否則輪替金鑰作業將會失敗。

若要旋轉金鑰，您必須擁有新金鑰的完整 url （請參閱[設定 Key Vault 和金鑰](#set-up-the-key-vault-and-keys)的步驟3）。 一旦您這樣做，請移至入口網站中的 [Kafka 叢集內容] 區段，然後按一下 [**磁片加密金鑰 URL**] 底下的 [**變更金鑰**]。 輸入新的金鑰 url 並提交以旋轉金鑰。

![Kafka 輪替磁片加密金鑰](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Apache Kafka 的 BYOK 常見問題集

**Kafka 叢集如何存取我的金鑰保存庫？**

在叢集建立期間，將受控識別與 HDInsight Kafka 叢集相關聯。 您可以在叢集建立之前或建立期間，建立這個受控識別。 您也需要對受控識別授與權限，使其能夠存取金鑰儲存所在的金鑰保存庫。

**這項功能是否適用於 HDInsight 上的所有 Kafka 叢集？**

BYOK 加密僅適用於 Kafka 1.1 和更新版本的叢集。

**不同主題/資料分割是否可以有不同金鑰？**

不行，叢集中的所有受控磁碟都要由相同金鑰加密。

**如果叢集失去對金鑰保存庫或金鑰的存取權，會發生什麼事？**

如果叢集失去金鑰的存取權，Apache Ambari 入口網站中將會顯示警告。 在此狀態下，**變更金鑰**作業將會失敗。 一旦還原金鑰存取權，Ambari 警告就會消失，而且可以順利執行金鑰輪替之類的作業。

![Apache Kafka 金鑰存取 Ambari 警示](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**如果金鑰已刪除，要如何復原叢集？**

因為只支援「虛刪除」啟用的金鑰，所以如果金鑰保存庫中的金鑰已復原，叢集應該會重新取得金鑰的存取權。 若要復原 Azure Key Vault 金鑰，請參閱 [復原-AzKeyVaultKeyRemova](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)l或[az-keyvault-Key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。

**是否可以讓生產者/消費者應用程式同時與 BYOK 叢集和非 BYOK 叢集搭配運作？**

可以。 生產者/消費者應用程式並不知道您有使用 BYOK。 加密會在 OS 層進行。 您不必對現有的生產者/消費者 Kafka 應用程式進行任何變更。

**是否也會加密 OS 磁碟/資源磁碟？**

不會。 作業系統磁片和資源磁片不會加密。

**如果將叢集相應增加，新的訊息代理程式是否也會順暢地支援 BYOK？**

可以。 叢集在相應增加期間需要存取金鑰保存庫中的金鑰。 叢集中的所有受控磁碟會使用同一個金鑰來加密。

**我所在的位置是否可以使用 BYOK？**

Kafka BYOK 適用於所有公用雲端。

## <a name="next-steps"></a>後續步驟

* 如需 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫](../../key-vault/key-vault-overview.md)？
* 若要開始使用 Azure Key Vault，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-overview.md)。
