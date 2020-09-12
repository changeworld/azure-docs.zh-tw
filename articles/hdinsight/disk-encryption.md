---
title: 靜態資料的雙重加密
titleSuffix: Azure HDInsight
description: 本文說明 Azure HDInsight 叢集中待用資料可用的兩層加密層級。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 97d899d73359cc45daf88940b815ed262c3b4766
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290832"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>靜態資料的 Azure HDInsight 雙重加密

本文討論 Azure HDInsight 叢集中待用資料加密的方法。 待用資料加密指的是在受控磁片上加密 (資料磁片、OS 磁片和暫存磁片) 連接到 HDInsight 叢集虛擬機器。 

本檔不會處理您 Azure 儲存體帳戶中儲存的資料。 您的叢集可能會有一或多個附加的 Azure 儲存體帳戶，其中加密金鑰也可以是 Microsoft 管理或由客戶管理，但加密服務不同。 如需 Azure 儲存體加密的詳細資訊，請參閱 [Azure 儲存體待用資料的加密](../storage/common/storage-service-encryption.md)。

## <a name="introduction"></a>簡介

Azure 中有三個主要受控磁片角色：資料磁片、OS 磁片和暫存磁片。 如需不同類型受控磁片的詳細資訊，請參閱 [Azure 受控磁片簡介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。 

HDInsight 支援兩種不同層級的多種加密類型：

- 伺服器端加密 (SSE) -SSE 是由儲存體服務執行。 在 HDInsight 中，會使用 SSE 來加密作業系統磁片和資料磁片。 這是預設啟用的通訊協定。 SSE 是第1層加密服務。
- 使用平臺管理的金鑰進行主機加密-類似于 SSE，儲存體服務會執行這種類型的加密。 不過，它只適用于暫存磁片，而且預設不會啟用。 主機的加密也是第1層加密服務。
- 使用客戶管理的金鑰進行待用加密-這種類型的加密可用於資料和暫存磁片。 預設不會啟用，而且需要客戶透過 Azure key vault 提供自己的金鑰。 靜態加密是第2層加密服務。

下表摘要說明這些類型。

|叢集類型 |OS 磁片 (受控磁片)  |資料磁片 (受控磁片)  |暫存資料磁片 (本機 SSD)  |
|---|---|---|---|
|Kafka，具有加速寫入的 HBase|Layer1：預設的[SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Layer1：預設的 [SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption) ，l2：使用 CMK 的選擇性靜態加密|Layer1：使用 PMK 的主機選擇性加密，L2：使用 CMK 的選擇性靜態加密|
|所有其他叢集 (Spark、互動式、Hadoop、HBase，而不需要加速寫入) |Layer1：預設的[SSE 加密](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|N/A|Layer1：使用 PMK 的主機選擇性加密，L2：使用 CMK 的選擇性靜態加密|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客戶管理的金鑰進行待用加密

客戶管理的金鑰加密是在叢集建立期間處理的一次性程式，不需額外費用。 您只需要使用 Azure Key Vault 授權受控識別，並在建立叢集時新增加密金鑰。

在叢集的每個節點上的資料磁片和暫存磁片都會使用對稱資料加密金鑰來加密 (DEK) 。 DEK 會使用金鑰保存庫中的金鑰加密金鑰 (KEK) 來加以保護。 加密和解密程序完全由 Azure HDInsight 來處理。

針對連接至叢集 Vm 的 OS 磁片， (PMK) 可用的一層加密。 如果您的案例需要 CMK 加密，建議客戶避免將機密資料複製到作業系統磁片。

如果在儲存磁片加密金鑰的金鑰保存庫上啟用金鑰保存庫防火牆，則必須將部署叢集所在區域的 HDInsight 區域資源提供者 IP 位址新增至金鑰保存庫防火牆設定。 這是必要的，因為 HDInsight 不是受信任的 Azure 金鑰保存庫服務。

您可以使用 Azure 入口網站或 Azure CLI，在金鑰保存庫中安全地輪替金鑰。 當金鑰旋轉時，HDInsight 叢集會在幾分鐘內開始使用新的金鑰。 啟用虛 [刪除](../key-vault/general/soft-delete-overview.md) 金鑰保護功能，以防止勒索軟體案例和意外刪除。 不支援不含此保護功能的金鑰保存庫。

### <a name="get-started-with-customer-managed-keys"></a>開始使用客戶管理的金鑰

若要建立已啟用客戶管理金鑰的 HDInsight 叢集，我們會進行下列步驟：

1. 建立適用于 Azure 資源的受控識別
1. 建立 Azure Key Vault
1. 建立金鑰
1. 建立存取原則
1. 建立已啟用客戶管理金鑰的 HDInsight 叢集
1. 輪替加密金鑰

下列其中一節將詳細說明每個步驟。

### <a name="create-managed-identities-for-azure-resources"></a>建立適用于 Azure 資源的受控識別

建立使用者指派的受控識別，以向 Key Vault 進行驗證。

如需特定步驟，請參閱 [建立使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 。 如需有關受控識別如何在 Azure HDInsight 中運作的詳細資訊，請參閱 [Azure HDInsight 中的受控](hdinsight-managed-identities.md)識別。 將受控識別資源識別碼新增至 Key Vault 存取原則時，請務必儲存起來。

### <a name="create-azure-key-vault"></a>建立 Azure Key Vault

建立金鑰保存庫。 如需特定步驟，請參閱 [建立 Azure Key Vault](../key-vault/secrets/quick-create-portal.md) 。

HDInsight 僅支援 Azure Key Vault。 如果您有自己的金鑰保存庫，則可以將自己的金鑰匯入 Azure Key Vault 中。 請記住，金鑰保存庫必須啟用虛 **刪除** 。 如需如何匯入現有金鑰的詳細資訊，請瀏覽[關於金鑰、祕密和憑證](../key-vault/about-keys-secrets-and-certificates.md)。

### <a name="create-key"></a>建立金鑰

1. 在新的金鑰保存庫中，流覽至 [**設定**機  >  **碼**  >  **+ 產生/匯入**]。

    ![在 Azure Key Vault 中產生新的金鑰](./media/disk-encryption/create-new-key.png "在 Azure Key Vault 中產生新的金鑰")

1. 提供名稱，然後選取 [ **建立**]。 維護**RSA**的預設**金鑰類型**。

    ![產生金鑰名稱](./media/disk-encryption/create-key.png "產生金鑰名稱")

1. 當您返回 [ **金鑰** ] 頁面時，請選取您建立的金鑰。

    ![key vault 金鑰清單](./media/disk-encryption/key-vault-key-list.png)

1. 選取版本以開啟 [ **金鑰版本** ] 頁面。 當您使用自己的金鑰進行 HDInsight 叢集加密時，您需要提供金鑰 URI。 複製**金鑰識別碼**並將其儲存到某處，直到您準備好建立叢集為止。

    ![取得金鑰識別碼](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>建立存取原則

1. 在新的金鑰保存庫中，流覽至 [**設定**  >  **存取**原則]  >  **+ [新增存取原則**]。

    ![建立新的 Azure Key Vault 存取原則](./media/disk-encryption/key-vault-access-policy.png)

1. 在 [ **新增存取原則** ] 頁面中，提供下列資訊：

    |屬性 |描述|
    |---|---|
    |金鑰許可權|選取 [ **取得**]、[解除包裝 **金鑰**] 和 [ **包裝金鑰**]。|
    |秘密許可權|選取 [ **取得**]、[ **設定**] 和 [ **刪除**]。|
    |選取主體|選取您稍早建立的使用者指派受控識別。|

    ![為 Azure Key Vault 存取原則設定 [選取主體]](./media/disk-encryption/azure-portal-add-access-policy.png)

1. 選取 [新增]。

1. 選取 [儲存]。

    ![儲存 Azure Key Vault 存取原則](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>使用客戶管理的金鑰磁片加密建立叢集

您現在可以開始建立新的 HDInsight 叢集。 客戶管理的金鑰只能在叢集建立期間套用至新的叢集。 無法從客戶管理的金鑰叢集移除加密，且客戶管理的金鑰無法新增至現有的叢集。

#### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

在叢集建立期間，請提供完整的 **金鑰識別碼**，包括金鑰版本。 例如： `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 。 您也需要將受控識別指派給叢集，並提供金鑰 URI。

![建立新的叢集](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>使用 Azure CLI

下列範例示範如何使用 Azure CLI 來建立啟用磁片加密的新 Apache Spark 叢集。 如需詳細資訊，請參閱 [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本

下列範例示範如何使用 Azure Resource Manager 範本，建立已啟用磁片加密的新 Apache Spark 叢集。 如需詳細資訊，請參閱 [什麼是 ARM 範本？](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)。

此範例會使用 PowerShell 來呼叫範本。

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

資源管理範本的內容 `azuredeploy.json` ：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>輪替加密金鑰

在某些情況下，您可能會想要在建立 HDInsight 叢集之後，變更其所使用的加密金鑰。 這可以透過入口網站輕鬆進行。 針對這項作業，叢集必須能夠存取目前的金鑰和預定的新金鑰，否則輪替金鑰作業將會失敗。

#### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要輪替金鑰，您需要基底金鑰保存庫 URI。 完成之後，請移至入口網站中的 [HDInsight 叢集屬性] 區段，然後按一下 [**磁片加密金鑰 URL**] 下的 [**變更金鑰**]。 輸入新的金鑰 url，並提交以旋轉金鑰。

![輪替磁片加密金鑰](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>使用 Azure CLI

下列範例顯示如何輪替現有 HDInsight 叢集的磁片加密金鑰。 如需詳細資訊，請參閱 [Azure CLI az hdinsight 旋轉-磁片-加密金鑰](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>客戶管理金鑰加密的常見問題

**HDInsight 叢集如何存取我的金鑰保存庫？**

HDInsight 會使用您與 HDInsight 叢集相關聯的受控識別來存取您的 Azure Key Vault 實例。 您可以在叢集建立之前或建立期間，建立這個受控識別。 您也需要對受控識別授與權限，使其能夠存取金鑰儲存所在的金鑰保存庫。

**這項功能是否適用于 HDInsight 上的所有叢集？**

客戶管理的金鑰加密適用于所有叢集類型，但 Spark 2.1 和2.2 除外。

**我可以使用多個金鑰來加密不同的磁片或資料夾嗎？**

否，所有受控磁片和資源磁片都是使用相同的金鑰來加密。

**如果叢集失去金鑰保存庫或金鑰的存取權，會發生什麼事？**

如果叢集失去金鑰的存取權，則會在 Apache Ambari 入口網站中顯示警告。 在此狀態下， **變更金鑰** 操作將會失敗。 還原金鑰存取後，Ambari 警告將會消失，而且可以成功執行金鑰輪替等作業。

![金鑰存取 Ambari 警示](./media/disk-encryption/ambari-alert.png)

**如果金鑰已刪除，要如何復原叢集？**

因為只支援「虛刪除」啟用的金鑰，所以如果金鑰已在金鑰保存庫中復原，則叢集應該會重新取得金鑰的存取權。 若要復原 Azure Key Vault 金鑰，請參閱 [復原-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 或 [az-keyvault-Key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)。


**如果相應增加叢集，新的節點是否能順暢地支援客戶管理的金鑰？**

可以。 叢集在相應增加期間需要存取金鑰保存庫中的金鑰。 使用相同的金鑰來加密叢集中的受控磁片和資源磁片。

**是否可在我的位置使用客戶管理的金鑰？**

HDInsight 客戶管理的金鑰適用于所有公用雲端和國家雲端。

## <a name="encryption-at-host-using-platform-managed-keys"></a>使用平臺管理的金鑰在主機上加密

### <a name="enable-in-the-azure-portal"></a>在 Azure 入口網站中啟用

在 Azure 入口網站中建立叢集期間，可以啟用主機的加密。

> [!Note]
> 在主機上啟用加密時，您無法從 Azure marketplace 將應用程式新增至您的 HDInsight 叢集。

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="在主機上啟用加密。":::

此選項會使用 PMK 啟用 HDInsight Vm 暫存資料磁片的 [主機加密](../virtual-machines/linux/disks-enable-host-based-encryption-portal.md) 。 在 [有限區域中的特定 VM sku 上僅支援](../virtual-machines/linux/disks-enable-host-based-encryption-portal.md) 在主機上進行加密，且 HDInsight 支援 [下列節點設定和 sku](./hdinsight-supported-node-configuration.md)。

若要瞭解 HDInsight 叢集的正確 VM 大小，請參閱為 [您的 Azure HDInsight 叢集選取適當的 vm 大小](hdinsight-selecting-vm-size.md)。 當啟用主機上的加密時，Zookeeper 節點的預設 VM SKU 將會 DS2V2。

### <a name="enable-using-powershell"></a>使用 PowerShell 啟用

下列程式碼片段說明如何建立新的 Azure HDInsight 叢集，該叢集在主機上使用 PowerShell 啟用加密。 它會使用參數 `-EncryptionAtHost $true` 來啟用此功能。

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>啟用使用 Azure CLI

下列程式碼片段說明如何使用 Azure CLI，建立在主機上啟用加密的新 Azure HDInsight 叢集。 它會使用參數 `--encryption-at-host true` 來啟用此功能。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Key Vault 的詳細資訊，請參閱 [Azure Key Vault 是什麼](../key-vault/general/overview.md)。
* [Azure HDInsight 中的企業安全性總覽](./domain-joined/hdinsight-security-overview.md)。
