---
title: 在 Azure 庫伯奈斯服務 （AKS） 中旋轉證書
description: 瞭解如何在 Azure 庫伯奈斯服務 （AKS） 群集中輪換證書。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: f299b13baf5811b92bdc2e40b027868617d7574c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368514"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 （AKS） 中旋轉證書

Azure 庫伯奈斯服務 （AKS） 使用證書進行許多元件的身份驗證。 出於安全或策略原因，您可能需要定期輪換這些證書。 例如，您可能有一個策略，可以每隔 90 天輪換所有證書。

本文介紹如何在 AKS 群集中輪換證書。

## <a name="before-you-begin"></a>開始之前

本文要求您運行 Azure CLI 版本 2.0.77 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 證書、憑證授權單位和服務帳戶

AKS 生成並使用以下證書、憑證授權單位和服務帳戶：

* AKS API 伺服器創建一個稱為群集 CA 的憑證授權單位 （CA）。
* API 伺服器具有群集 CA，用於從 API 伺服器到 kubelet 的單向通信證書。
* 每個庫貝萊特還創建一個證書簽名請求 （CSR），該請求由群集 CA 簽名，用於從庫貝萊特到 API 伺服器的通信。
* 蝕刻鍵值存儲具有由群集 CA 簽名的證書，用於從蝕刻到 API 伺服器的通信。
* 蝕刻鍵值存儲創建一個 CA，用於對證書進行簽名，以在 AKS 群集中的蝕刻副本之間驗證和授權資料複製。
* API 聚合器使用群集 CA 頒發證書以與其他 API（如 Azure 的打開服務代理）通信。 API 聚合器還可以有自己的 CA 來頒發這些證書，但它當前使用群集 CA。
* 每個節點使用由群集 CA 簽名的服務帳戶 （SA） 權杖。
* 用戶端`kubectl`具有用於與 AKS 群集通信的證書。

> [!NOTE]
> 在 2019 年 3 月之前創建的 AKS 群集具有兩年後過期的證書。 2019 年 3 月之後創建的任何群集或任何已旋轉其證書的群集都具有群集 CA 憑證，這些證書將在 30 年後過期。 所有其他證書將在兩年後過期。 要驗證群集的創建時間，請使用`kubectl get nodes`以查看節點池*的年齡*。
> 
> 此外，還可以檢查群集證書的到期日期。 例如，以下命令顯示*myAKSCluster 群集*的證書詳細資訊。
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>旋轉群集證書

> [!WARNING]
> 使用`az aks rotate-certs`旋轉證書可能會導致 AKS 群集最多 30 分鐘的停機時間。

使用[az aks 獲取憑據][az-aks-get-credentials]登錄到 AKS 群集。 此命令還會在本地電腦上下載和配置`kubectl`用戶端憑證。

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

用於`az aks rotate-certs`旋轉群集上的所有證書、C 和 SA。

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> 完成可能需要 30 分鐘`az aks rotate-certs`。 如果命令在完成之前失敗，請使用`az aks show`以驗證群集的狀態為*證書旋轉*。 如果群集處於失敗狀態，請重新運行`az aks rotate-certs`以再次旋轉證書。

通過運行`kubectl`命令驗證舊證書是否不再有效。 由於您尚未更新 所使用的`kubectl`證書，因此您將看到一個錯誤。  例如：

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

更新 運行`az aks get-credentials`使用的`kubectl`證書。

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

通過運行`kubectl`命令驗證憑證已更新，該命令現在將成功。 例如：

```console
kubectl get no
```

> [!NOTE]
> 如果您有在 AKS 上運行的任何服務（如[Azure 開發人員空間][dev-spaces]），則可能需要[更新與這些服務相關的證書][dev-spaces-rotate]。

## <a name="next-steps"></a>後續步驟

本文介紹如何自動輪換群集的證書、C 和 SA。 有關 AKS 安全最佳實踐的詳細資訊，請參閱[Azure 庫伯奈斯服務 （AKS） 中群集安全和升級][aks-best-practices-security-upgrades]的最佳做法。


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
