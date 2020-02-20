---
title: 在 Azure Kubernetes Service 中輪替憑證（AKS）
description: 瞭解如何在 Azure Kubernetes Service （AKS）叢集中輪替您的憑證。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 774a0354c6262598c7d5e1f51e2e475fd17fe2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468275"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中輪替憑證（AKS）

Azure Kubernetes Service （AKS）會使用憑證來驗證其許多元件。 基於安全性或原則的理由，您可能需要定期輪替這些憑證。 例如，您可能會有一個原則，每90天輪替一次您的憑證。

本文說明如何在 AKS 叢集中輪替憑證。

## <a name="before-you-begin"></a>開始之前

本文會要求您執行 Azure CLI 版本2.0.77 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 憑證、憑證授權單位單位和服務帳戶

AKS 會產生並使用下列憑證、憑證授權單位單位和服務帳戶：

* AKS API 伺服器會建立名為叢集 CA 的憑證授權單位單位（CA）。
* API 伺服器具有叢集 CA，它會簽署從 API 伺服器到 kubelets 的單向通訊憑證。
* 每個 kubelet 也會建立由叢集 CA 簽署的憑證簽署要求（CSR），以便從 kubelet 到 API 伺服器的通訊。
* Etcd 金鑰值存放區具有叢集 CA 簽署的憑證，可從 etcd 至 API 伺服器進行通訊。
* Etcd 金鑰值存放區會建立簽署憑證的 CA，以驗證和授權 AKS 叢集中 etcd 複本之間的資料複寫。
* API 匯總工具會使用叢集 CA 來發行憑證，以便與其他 Api （例如 Azure 的 Open Service Broker）進行通訊。 API 匯總工具也可以有自己的 CA 來頒發證書，但目前使用的是叢集 CA。
* 每個節點都會使用由叢集 CA 簽署的服務帳戶（SA）權杖。
* `kubectl` 用戶端具有與 AKS 叢集通訊的憑證。

> [!NOTE]
> 在2019年3月之前建立的 AKS 叢集，其憑證會在兩年後到期。 2019年3月之後建立的任何叢集，或任何已輪替其憑證的叢集，其憑證會在30年後到期。 若要確認您的叢集建立時間，請使用 `kubectl get nodes` 查看節點集區的*存留期*。
> 
> 此外，您可以檢查叢集憑證的到期日。 例如，下列命令會顯示*myAKSCluster*叢集的憑證詳細資料。
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>旋轉您的叢集憑證

> [!WARNING]
> 使用 `az aks rotate-certs` 輪替憑證可能會導致您的 AKS 叢集停機時間最長為30分鐘。

使用[az aks get-認證][az-aks-get-credentials]來登入您的 aks 叢集。 此命令也會在您的本機電腦上下載並設定 `kubectl` 用戶端憑證。

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

使用 `az aks rotate-certs` 來輪替您叢集上的所有憑證、Ca 和 SAs。

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> 最多可能需要30分鐘的時間，才能完成 `az aks rotate-certs`。 如果命令在完成之前失敗，請使用 `az aks show` 來確認叢集的狀態為 [*憑證輪替*]。 如果叢集處於失敗狀態，請重新執行 `az aks rotate-certs` 以再次輪替您的憑證。

執行 `kubectl` 命令，確認舊憑證已不再有效。 由於您尚未更新 `kubectl`所使用的憑證，因此您會看到錯誤。  例如：

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

藉由執行 `az aks get-credentials`，更新 `kubectl` 所使用的憑證。

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

藉由執行 `kubectl` 命令來確認憑證已更新，這現在將會成功。 例如：

```console
kubectl get no
```

## <a name="next-steps"></a>後續步驟

本文說明如何自動輪替叢集的憑證、Ca 和 SAs。 如需 AKS 安全性最佳做法的詳細資訊，請參閱[Azure Kubernetes Service 中叢集安全性和升級的最佳做法（AKS）][aks-best-practices-security-upgrades] 。


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
