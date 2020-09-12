---
title: 在 Azure Red Hat OpenShift 4 叢集中新增或更新您的 Red Hat 提取秘密
description: 在現有的 4.x ARO 叢集上新增或更新您的 Red Hat 提取秘密
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: 提取秘密、aro、openshift、red hat
ms.openlocfilehash: 769b7589fb6496fc2f4123665ad1f6fe61d0cce2
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294742"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 叢集中新增或更新您的 Red Hat 提取秘密

本指南說明如何為現有的 Azure Red Hat OpenShift (ARO) 4.x 叢集新增或更新您的 Red Hat 提取秘密。

如果您是第一次建立叢集，您可以在建立叢集時新增提取秘密。 如需有關使用 Red Hat 提取秘密建立 ARO 叢集的詳細資訊，請參閱 [建立 Azure Red Hat OpenShift 4](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)叢集。

## <a name="before-you-begin"></a>開始之前

本指南假設您已有現有的 Azure Red Hat OpenShift 4 叢集。 確定您具有叢集的系統管理員存取權。

## <a name="prepare-your-pull-secret"></a>準備您的提取密碼
當您建立 ARO 叢集但未新增 Red Hat pull 秘密時，仍會自動在您的叢集上建立提取秘密。 不過，此提取秘密未完整填入。

本節將逐步解說如何以您的 Red Hat pull 秘密中的其他值來更新該提取秘密。

1. 藉 `pull-secret` `openshift-config` 由執行下列命令，提取命名空間中命名的秘密，並將它儲存至不同的檔案： 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    您的輸出應該如下所示。  (請注意，已移除實際的秘密值。 ) 

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. 移至您的 [Red Hat OpenShift cluster manager 入口網站](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ，然後選取 [ **下載提取密碼**]。 您的 Red Hat 提取密碼看起來會像下面這樣。  (請注意，已移除實際的秘密值。 ) 

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. 藉由新增在您的 Red Hat 提取秘密中找到的專案，編輯您從叢集取得的提取秘密檔案。 

    > [!IMPORTANT]
    > 包含 `cloud.openshift.com` 來自 Red hat 提取秘密的專案，會導致您的叢集開始將遙測資料傳送至 Red hat。 只有當您想要傳送遙測資料時，才包含此區段。 否則，請讓下一節退出。    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > 請勿從您的提取秘密移除或修改您的 `arosvc.azurecr.io` 專案。 需要此區段才能讓您的叢集正常運作。

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    您的最終檔案看起來應該如下所示。  (請注意，已移除實際的秘密值。 ) 

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. 確定檔案是有效的 JSON。 有許多方式可驗證 JSON。 下列範例會使用 jq：

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > 如果檔案中有錯誤，則會顯示為 `parse error` 。

## <a name="add-your-pull-secret-to-your-cluster"></a>將您的提取秘密新增至叢集

執行下列命令以更新您的提取秘密。

> [!NOTE]
> 執行此命令會導致您的叢集節點在更新時一併重新開機。 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

設定秘密之後，您就可以啟用 Red Hat 認證操作員。

### <a name="modify-the-configuration-files"></a>修改設定檔

修改下列物件以啟用 Red Hat 運算子。

首先，修改範例操作員設定檔。 然後，您可以執行下列命令來編輯設定檔：

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

將 `spec.architectures.managementState` 和 `status.architecture.managementState` 值從變更 `Removed` 為 `Managed` 。 

下列 YAML 程式碼片段只會顯示已編輯 YAML 檔案的相關區段：

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

其次，執行下列命令以編輯操作員中樞設定檔：  

```console
oc edit operatorhub cluster -o yaml
```

`Spec.Sources.Disabled` `Status.Sources.Disabled` `true` `false` 針對您想要啟用的任何來源，將和值從變更為。

下列 YAML 程式碼片段只會顯示已編輯 YAML 檔案的相關區段：

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

儲存檔案以套用您的編輯。

## <a name="validate-that-your-secret-is-working"></a>驗證您的秘密是否正常運作

新增您的提取密碼並修改正確的設定檔案之後，您的叢集可能需要幾分鐘的時間才會更新。 若要檢查您的叢集是否已更新，請執行下列命令來顯示已認證的操作員和可用的 Red Hat 運算子來源：

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

如果您沒有看到經認證的操作員和 Red Hat 運算子，請稍候幾分鐘，然後再試一次。

為確保您的提取密碼已更新且正常運作，請開啟 OperatorHub，並檢查是否有任何 Red Hat 驗證的操作員。 例如，檢查 OpenShift 容器儲存體操作員是否可用，並查看您是否有安裝的許可權。

## <a name="next-steps"></a>接下來的步驟
若要深入瞭解 Red Hat 提取秘密，請參閱 [使用映射提取秘密](https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html)。

若要深入瞭解 Red Hat OpenShift 4，請參閱 [Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/welcome/index.html)。
