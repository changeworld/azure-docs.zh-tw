---
title: 在 Azure Red Hat OpenShift 4 上建立 Azure 檔案儲存體 StorageClass
description: 瞭解如何建立 Azure Red Hat OpenShift 上的 Azure 檔案儲存體 StorageClass
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro、openshift、az aro、red hat、cli、azure 檔案
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fe80698b71ae0ba808991d79b423d49abfacdf7c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825928"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>在 Azure Red Hat OpenShift 4 上建立 Azure 檔案儲存體 StorageClass

在本文中，您將建立 Azure Red Hat OpenShift 4 的 StorageClass，以使用 Azure 檔案儲存體動態布建 ReadWriteMany (RWX) 儲存體。 您將了解如何：

> [!div class="checklist"]
> * 設定必要條件，並安裝必要的工具
> * 使用 Azure File 布建程式建立 Azure Red Hat OpenShift 4 StorageClass

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>開始之前

將 Azure Red Hat OpenShift 4 叢集部署到您的訂用帳戶中，請參閱[建立 Azure Red Hat OpenShift 4](tutorial-create-cluster.md)叢集


### <a name="set-up-azure-storage-account"></a>設定 Azure 儲存體帳戶

此步驟會在 Azure Red Hat OpenShift (ARO) 叢集的資源群組之外建立資源群組。 此資源群組會包含 Azure Red Hat OpenShift 的動態布建程式所建立的 Azure 檔案儲存體共用。

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>設定權限
### <a name="set-resource-group-permissions"></a>設定資源群組許可權

ARO 服務主體需要新 Azure 儲存體帳戶資源群組的 ' listKeys ' 許可權。 指派「參與者」角色以達成此目的。

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>設定 ARO 叢集許可權

OpenShift 永久性磁片區系結器服務帳戶將需要能夠讀取秘密。 建立並指派 OpenShift 叢集角色以達成此目的。
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>使用 Azure 檔案儲存體布建程式建立 StorageClass

此步驟會建立具有 Azure 檔案儲存體布建程式的 StorageClass。 在 StorageClass 資訊清單中，需要儲存體帳戶的詳細資料，讓 ARO 叢集知道要查看目前資源群組以外的儲存體帳戶。

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>變更預設 StorageClass (選用) 

ARO 的預設 StorageClass 稱為受控-premium，並使用 azure 磁片布建程式。 針對 StorageClass 資訊清單發出修補程式命令來變更此項。

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>確認 Azure 檔案 StorageClass (選用) 

建立新的應用程式，並將儲存體指派給它。

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
您也可以透過 Azure 入口網站中的儲存體總管來顯示 test.txt 檔案。

## <a name="next-steps"></a>後續步驟

在本文中，您已使用 Microsoft Azure 檔案和 Azure Red Hat OpenShift 4 建立動態持續性儲存體。 您已了解如何︰

> [!div class="checklist"]
> * 建立儲存體帳戶
> * 使用 Azure 檔案儲存體布建程式在 Azure Red Hat OpenShift 4 叢集上設定 StorageClass

前進到下一篇文章，以瞭解 Azure Red Hat OpenShift 4 支援的資源。

* [Azure Red Hat OpenShift 支援原則](support-policies-v4.md)
