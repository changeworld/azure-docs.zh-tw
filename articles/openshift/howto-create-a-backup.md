---
title: 使用 Velero 建立 Azure Red Hat OpenShift 4 叢集應用程式備份
description: 瞭解如何使用 Velero 建立 Azure Red Hat OpenShift 叢集應用程式的備份
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 49ffc33310564299131e2831b74154719b7cf7c7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078573"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>建立 Azure Red Hat OpenShift 4 叢集應用程式備份

在本文中，您將準備您的環境，以建立 Azure Red Hat OpenShift 4 叢集應用程式備份。 您將學習如何：

> [!div class="checklist"]
> * 設定必要條件，並安裝必要的工具
> * 建立 Azure Red Hat OpenShift 4 應用程式備份

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>開始之前

### <a name="install-velero"></a>安裝 Velero

若要在您的系統上 [安裝](https://velero.io/docs/main/basic-install/) Velero，請遵循適用于您作業系統的建議程式。

### <a name="set-up-azure-storage-account-and-blob-container"></a>設定 Azure 儲存體帳戶和 Blob 容器

此步驟會在 ARO 叢集的資源群組之外建立資源群組。  此資源群組可讓備份持續保存，並可將應用程式還原至新的叢集。

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>設定 Velero 的許可權

### <a name="create-service-principal"></a>建立服務主體

Velero 需要備份和還原的許可權。 當您建立服務主體時，您會授與 Velero 許可權，以存取您在上一個步驟中定義的資源群組。 此步驟會取得叢集的資源群組：

```bash
export AZURE_RESOURCE_GROUP=aro-$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r '.clusterProfile.domain')
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 叢集上安裝 Velero

此步驟會將 Velero 安裝到自己的專案，以及使用 Velero 進行備份和還原所需的 [自訂資源定義](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) 。 請確定您已成功登入 Azure Red Hat OpenShift v4 叢集。


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>使用 Velero 建立備份

若要使用 Velero 建立應用程式備份，您必須包含此應用程式所在的命名空間。  如果您有 `nginx-example` 命名空間，而且想要在備份中包含該命名空間中的所有資源，請在終端機中執行下列命令：

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
您可以執行下列動作來檢查備份的狀態：

```bash
oc get backups -n velero <name of backup> -o yaml
```

成功的備份將會輸出 `phase:Completed` ，而物件將會存留在儲存體帳戶的容器中。

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>使用 Velero 建立備份以包含快照集

若要使用 Velero 建立應用程式備份以包含應用程式的持續性磁片區，您必須包含應用程式所在的命名空間，以及在 `snapshot-volumes=true` 建立備份時包含旗標。

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

您可以執行下列動作來檢查備份的狀態：

```bash
oc get backups -n velero <name of backup> -o yaml
```

具有輸出的成功備份 `phase:Completed` 和物件會存留于儲存體帳戶的容器中。

如需如何使用 Velero 建立備份和還原的詳細資訊，請參閱以 [原生方式備份 OpenShift 資源](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>後續步驟

在本文中，Azure Red Hat OpenShift 4 叢集應用程式已備份。 您已了解如何︰

> [!div class="checklist"]
> * 使用 Velero 建立 OpenShift v4 叢集應用程式備份
> * 使用 Velero 建立具有快照集的 OpenShift v4 叢集應用程式備份


前進到下一篇文章，以瞭解如何建立 Azure Red Hat OpenShift 4 叢集應用程式還原。

* [建立 Azure Red Hat OpenShift 4 叢集應用程式還原](howto-create-a-restore.md)
* [建立 Azure Red Hat OpenShift 4 叢集應用程式還原，包括快照集](howto-create-a-restore.md)
