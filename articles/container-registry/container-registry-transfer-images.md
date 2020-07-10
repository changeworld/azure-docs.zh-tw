---
title: 傳輸構件
description: 使用 Azure 儲存體帳戶建立傳輸管線，以將映射或其他成品的集合從一個容器登錄傳送至另一個登錄
ms.topic: article
ms.date: 05/08/2020
ms.custom: ''
ms.openlocfilehash: c80f10e8795c63b84bb46fc21fd3406a195b772e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186923"
---
# <a name="transfer-artifacts-to-another-registry"></a>將成品傳輸至另一個登錄

本文說明如何將映射或其他登錄成品的集合，從一個 Azure container registry 轉移至另一個登錄。 來源和目標登錄可以位於相同或不同的訂用帳戶中，Active Directory 租使用者、Azure 雲端或實體中斷連線的雲端。 

若要傳送成品，您可以使用[blob 儲存體](../storage/blobs/storage-blobs-introduction.md)建立*傳輸管線*，以在兩個登錄之間複寫成品：

* 來源登錄中的成品會匯出至來源儲存體帳戶中的 blob 
* Blob 會從來源儲存體帳戶複製到目標儲存體帳戶
* 目標儲存體帳戶中的 blob 會在目標登錄中匯入為成品。 當目標儲存體中的成品 blob 更新時，您可以設定要觸發的匯入管線。

轉移非常適合用來在實體中斷連線的雲端中的兩個 Azure container registry 之間複製內容，並在每個雲端中的儲存體帳戶 mediated。 若為已連線雲端（包括 Docker Hub 和其他雲端廠商）中的容器登錄進行映射複製，則建議改為匯[入映射](container-registry-import-images.md)。

在本文中，您會使用 Azure Resource Manager 範本部署來建立和執行傳輸管線。 Azure CLI 可用來布建相關聯的資源，例如儲存體秘密。 建議使用 Azure CLI 版2.2.0 或更新版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI][azure-cli]。

**進階**容器登錄服務層級中提供這項功能。 如需登錄服務層級和限制的相關資訊，請參閱 [Azure Container Registry 層級](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>必要條件

* **容器**登錄-您需要具有要傳送之成品的現有來源登錄，以及目標登錄。 ACR 轉移適用于實體中斷連線的雲端之間的移動。 若要進行測試，來源和目標登錄可以位於相同或不同的 Azure 訂用帳戶、Active Directory 租使用者或雲端。 如果您需要建立登錄，請參閱[快速入門：使用 Azure CLI 建立私人容器](container-registry-get-started-azure-cli.md)登錄。 
* **儲存體帳戶**-在訂用帳戶和您選擇的位置中建立來源和目標儲存體帳戶。 基於測試目的，您可以使用與來源和目標登錄相同的訂用帳戶或訂閱。 針對跨雲端案例，通常您會在每個雲端中建立個別的儲存體帳戶。 如有需要，請使用[Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli)或其他工具來建立儲存體帳戶。 

  為每個帳戶中的成品傳輸建立 blob 容器。 例如，建立一個名為*transfer*的容器。 有兩個或多個傳輸管線可以共用相同的儲存體帳戶，但應使用不同的儲存體容器範圍。
* **金鑰保存庫**-儲存用來存取來源和目標儲存體帳戶的 SAS 權杖秘密時，需要 key vault。 在與您的來源和目標登錄相同的 Azure 訂用帳戶中，建立來源和目標金鑰保存庫。 如有需要，請使用[Azure CLI](../key-vault/quick-create-cli.md)或其他工具建立金鑰保存庫。
* **環境變數**-例如本文中的命令，設定來源和目標環境的下列環境變數。 所有範例都是針對 Bash shell 進行格式化。
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>案例概觀

您會建立下列三個管線資源，以在登錄之間進行映射傳輸。 所有都是使用 PUT 作業來建立。 這些資源會在您的*來源*和*目標*登錄和儲存體帳戶上運作。 

儲存體驗證使用 SAS 權杖，做為金鑰保存庫中的秘密來管理。 管線會使用受控識別來讀取保存庫中的秘密。

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** -長期的資源，包含有關*來源*登錄和儲存體帳戶的高階資訊。 這項資訊包括來源儲存體 blob 容器 URI 和管理來源 SAS 權杖的金鑰保存庫。 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** -長期持久的資源，包含*目標*登錄和儲存體帳戶的高階資訊。 這項資訊包括目標儲存體 blob 容器 URI，以及管理目標 SAS 權杖的金鑰保存庫。 預設會啟用匯入觸發程式，因此當成品 blob 落在目標儲存體容器時，管線就會自動執行。 
* **[來擷取](#create-pipelinerun-for-export-with-resource-manager)**-用來叫用 ExportPipeline 或 ImportPipeline 資源的資源。  
  * 您可以藉由建立來擷取資源並指定要匯出的成品，手動執行 ExportPipeline。  
  * 如果已啟用匯入觸發程式，ImportPipeline 會自動執行。 也可以使用來擷取來手動執行。 
  * 目前，每個來擷取最多可以傳輸**50**個成品。

### <a name="things-to-know"></a>須知事項
* ExportPipeline 和 ImportPipeline 通常會在與來源和目的地雲端相關聯的不同 Active Directory 租使用者中。 此案例需要不同的受控識別和金鑰保存庫，才能匯出和匯入資源。 基於測試目的，這些資源可以放在相同的雲端中，並共用身分識別。
* 管線範例會建立系統指派的受控識別來存取金鑰保存庫密碼。 ExportPipelines 和 ImportPipelines 也支援使用者指派的身分識別。 在此情況下，您必須使用身分識別的存取原則來設定金鑰保存庫。 

## <a name="create-and-store-sas-keys"></a>建立和儲存 SAS 金鑰

傳輸會使用共用存取簽章 (SAS) 權杖來存取來源和目標環境中的儲存體帳戶。 產生並儲存權杖，如下列各節所述。  

### <a name="generate-sas-token-for-export"></a>產生用於匯出的 SAS 權杖

執行[az storage container 產生-sas][az-storage-container-generate-sas]命令，為來源儲存體帳戶中的容器產生 sas 權杖，用於成品匯出。

*建議的權杖許可權*：讀取、寫入、列出、新增。 

在下列範例中，命令輸出會指派給 EXPORT_SAS 環境變數，並在前面加上 '？ ' 字元。 更新 `--expiry` 您環境的值：

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>儲存用於匯出的 SAS 權杖

使用[az keyvault secret set][az-keyvault-secret-set]將 SAS 權杖儲存在來源 Azure 金鑰保存庫中：

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>產生用於匯入的 SAS 權杖

執行[az storage container 產生-sas][az-storage-container-generate-sas]命令，為目標儲存體帳戶中的容器產生 sas 權杖，用於成品匯入。

*建議的權杖許可權*： [讀取]、[刪除]、[清單]

在下列範例中，命令輸出會指派給 IMPORT_SAS 環境變數，並在前面加上 '？ ' 字元。 更新 `--expiry` 您環境的值：

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>儲存用於匯入的 SAS 權杖

使用[az keyvault secret set][az-keyvault-secret-set]將 SAS 權杖儲存在目標 Azure 金鑰保存庫中：

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>使用 Resource Manager 建立 ExportPipeline

使用 Azure Resource Manager 範本部署，為您的來源容器登錄建立 ExportPipeline 資源。

將 ExportPipeline Resource Manager[範本](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines)檔案複製到本機資料夾。

在檔案中輸入下列參數值 `azuredeploy.parameters.json` ：

|參數  |值  |
|---------|---------|
|于： registryname     | 來源容器登錄的名稱      |
|exportPipelineName     |  您為匯出管線選擇的名稱       |
|targetUri     |  來源環境中儲存體容器的 URI (匯出管線) 的目標。<br/>範例： `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  來源金鑰保存庫的名稱  |
|sasTokenSecretName  | 來源金鑰保存庫中的 SAS 權杖秘密名稱 <br/>範例： acrexportsas

### <a name="export-options"></a>匯出選項

`options`匯出管線的屬性支援選擇性的布林值。 建議使用下列值：

|參數  |值  |
|---------|---------|
|選項 | OverwriteBlobs-覆寫現有的目標 blob<br/>ContinueOnErrors-如果某個成品匯出失敗，則繼續匯出來源登錄中的其餘成品。

### <a name="create-the-resource"></a>建立資源

執行[az deployment group create][az-deployment-group-create]以建立資源。 下列範例會將部署*exportPipeline*命名為。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

在命令輸出中，記下管線的資源識別碼 (`id`) 。 您可以執行[az deployment group show][az-deployment-group-show]，將此值儲存在環境變數中供稍後使用。 例如：

```azurecli
EXPORT_RES_ID=$(az group deployment show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>使用 Resource Manager 建立 ImportPipeline 

使用 Azure Resource Manager 範本部署，在目標容器登錄中建立 ImportPipeline 資源。 根據預設，當目標環境中的儲存體帳戶具有成品 blob 時，管線會自動匯入。

將 ImportPipeline Resource Manager[範本](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines)檔案複製到本機資料夾。

在檔案中輸入下列參數值 `azuredeploy.parameters.json` ：

參數  |值  |
|---------|---------|
|于： registryname     | 目標容器登錄的名稱      |
|importPipelineName     |  您為匯入管線選擇的名稱       |
|sourceUri     |  目標環境中儲存體容器的 URI (匯入管線) 的來源。<br/>範例： `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  目標金鑰保存庫的名稱 |
|sasTokenSecretName     |  目標金鑰保存庫中的 SAS 權杖秘密名稱<br/>範例： acr importsas |

### <a name="import-options"></a>匯入選項

匯 `options` 入管線的屬性支援選擇性的布林值。 建議使用下列值：

|參數  |值  |
|---------|---------|
|選項 | OverwriteTags-覆寫現有的目標標記<br/>DeleteSourceBlobOnSuccess-成功匯入目標登錄之後，刪除來源儲存體 blob<br/>ContinueOnErrors-如果某個成品匯入失敗，則繼續匯入目標登錄中的剩餘成品。

### <a name="create-the-resource"></a>建立資源

執行[az deployment group create][az-deployment-group-create]以建立資源。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json \
  --name importPipeline
```

如果您打算手動執行匯入，請記下管線的資源識別碼 (`id`) 。 您可以執行[az deployment group show][az-deployment-group-show]，將此值儲存在環境變數中供稍後使用。 例如：

```azurecli
IMPORT_RES_ID=$(az group deployment show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>使用 Resource Manager 建立匯出的來擷取 

使用 Azure Resource Manager 範本部署，為您的來源容器登錄建立來擷取資源。 此資源會執行您先前建立的 ExportPipeline 資源，並將指定的成品從容器登錄匯出為來源儲存體帳戶的 blob。

將來擷取 Resource Manager[範本](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export)檔案複製到本機資料夾。

在檔案中輸入下列參數值 `azuredeploy.parameters.json` ：

|參數  |值  |
|---------|---------|
|于： registryname     | 來源容器登錄的名稱      |
|pipelineRunName     |  您為執行選擇的名稱       |
|pipelineResourceId     |  匯出管線的資源識別碼。<br/>範例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  您為匯出至來源儲存體帳戶的成品 blob 選擇的名稱，例如*myblob*
|artifacts | 要傳送的來源成品陣列，做為標記或資訊清單摘要<br/>範例： `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

執行[az deployment group create][az-deployment-group-create]來建立來擷取資源。 下列範例會將部署*exportPipelineRun*命名為。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

可能需要幾分鐘的時間才能匯出成品。 當部署順利完成時，請在來源儲存體帳戶的*傳輸*容器中列出匯出的 blob，以確認成品匯出。 例如，執行[az storage blob list][az-storage-blob-list]命令：

```azurecli
az storage blob list \
  --account-name $SA_SOURCE
  --container transfer
  --output table
```

## <a name="transfer-blob-optional"></a>傳送 blob (選擇性)  

使用 AzCopy 工具或其他方法，將[blob 資料](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts)從來源儲存體帳戶傳送到目標儲存體帳戶。

例如，下列命令會 [`azcopy copy`](/azure/storage/common/storage-ref-azcopy-copy) 將 myblob 從來源帳戶中的*傳輸*容器複製到目標帳戶中的*傳輸*容器。 如果 blob 存在於目標帳戶中，則會予以覆寫。 驗證會針對來源和目標容器使用具有適當許可權的 SAS 權杖。 不會顯示建立權杖的 (步驟。 ) 

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>觸發 ImportPipeline 資源

如果您已啟用 `sourceTriggerStatus` ImportPipeline 的參數 (預設值) ，則會在將 blob 複製到目標儲存體帳戶之後觸發管線。 匯入成品可能需要幾分鐘的時間。 當匯入成功完成時，請列出目標容器登錄中的存放庫，以確認成品匯入。 例如，執行[az acr repository list][az-acr-repository-list]：

```azurecli
az acr repository list --name <target-registry-name>
```

如果您未啟用匯 `sourceTriggerStatus` 入管線的參數，請手動執行 ImportPipeline 資源，如下一節所示。 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>建立來擷取以匯入 Resource Manager (選擇性)  
 
您也可以使用來擷取資源來觸發 ImportPipeline，以將成品匯入至目標容器登錄。

將來擷取 Resource Manager[範本](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import)檔案複製到本機資料夾。

在檔案中輸入下列參數值 `azuredeploy.parameters.json` ：

|參數  |值  |
|---------|---------|
|于： registryname     | 目標容器登錄的名稱      |
|pipelineRunName     |  您為執行選擇的名稱       |
|pipelineResourceId     |  匯入管線的資源識別碼。<br/>範例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  儲存體帳戶中所匯出成品的現有 blob 名稱，例如*myblob*

執行[az deployment group create][az-deployment-group-create]來執行資源。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

成功完成部署時，請列出目標容器登錄中的存放庫，以確認成品匯入。 例如，執行[az acr repository list][az-acr-repository-list]：

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="delete-pipeline-resources"></a>刪除管線資源

若要刪除管線資源，請使用[az deployment group delete][az-deployment-group-delete]命令來刪除其 Resource Manager 部署。 下列範例會刪除在本文中建立的管線資源：

```azurecli
az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipeline

az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun

az deployment group delete \
  --resource-group $TARGET_RG \
  --name importPipeline  
```

## <a name="troubleshooting"></a>疑難排解

* **範本部署失敗或錯誤**
  * 如果管線執行失敗，請查看 `pipelineRunErrorMessage` 執行資源的屬性。
  * 如需常見的範本部署錯誤，請參閱針對[ARM 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **匯出或匯入儲存體 blob 的問題**
  * SAS 權杖可能已過期，或其許可權不足，無法進行指定的匯出或匯入執行
  * 在多個匯出執行期間，可能不會覆寫來源儲存體帳戶中的現有儲存體 blob。 確認 OverwriteBlob 選項是在匯出執行中設定，且 SAS 權杖具有足夠的許可權。
  * 成功匯入執行之後，可能無法刪除目標儲存體帳戶中的儲存體 blob。 確認已在匯入回合中設定 DeleteBlobOnSuccess 選項，且 SAS 權杖具有足夠的許可權。
  * 儲存體 blob 未建立或刪除。 確認匯出或匯入回合中指定的容器存在，或指定的儲存體 blob 存在，以供手動匯入執行。 
* **AzCopy 問題**
  * 請參閱針對[AzCopy 問題進行疑難排解](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues)。  
* **成品傳輸問題**
  * 並非所有成品（或無）都會傳送。 確認匯出回合中成品的拼寫，以及匯出和匯入執行中的 blob 名稱。 確認您要傳輸最多50個成品。
  * 管線執行可能尚未完成。 匯出或匯入執行可能需要一些時間。 
  * 針對其他管線問題，請提供匯出執行或匯入執行的部署相互[關聯識別碼](../azure-resource-manager/templates/deployment-history.md)給 Azure Container Registry 小組。


## <a name="next-steps"></a>後續步驟

若要從公用登錄或其他私人登錄將單一容器映射匯入至 Azure container registry，請參閱[az acr import][az-acr-import]命令參考。

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import



