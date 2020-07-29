---
title: 檢查登錄健全狀況
description: 瞭解如何執行快速診斷命令，以識別使用 Azure container registry 時的常見問題，包括本機 Docker 設定和登錄的連線能力。
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74456404"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>檢查 Azure container registry 的健全狀況

使用 Azure container registry 時，您可能偶爾會遇到問題。 例如，您可能無法提取容器映射，因為您的本機環境中有 Docker 的問題。 或者，網路問題可能會讓您無法連接到登錄。 

作為第一個診斷步驟，請執行[az acr check-health][az-acr-check-health]命令，以取得環境健全狀況的相關資訊，並選擇性地存取目標登錄。 此命令適用于 Azure CLI 版本2.0.67 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="run-az-acr-check-health"></a>執行 az acr check-health

下列範例顯示執行命令的不同方式 `az acr check-health` 。

> [!NOTE]
> 如果您在 Azure Cloud Shell 中執行命令，則不會檢查本機環境。 不過，您可以檢查目標登錄的存取權。

### <a name="check-the-environment-only"></a>僅檢查環境

若要檢查本機 Docker daemon、CLI 版本和 Helm 用戶端設定，請執行命令，而不需要額外的參數：

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>檢查環境和目標登錄

若要檢查登錄的存取權，以及執行本機環境檢查，請傳遞目標登錄的名稱。 例如：

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>錯誤報告

命令會將資訊記錄到標準輸出。 如果偵測到問題，則會提供錯誤碼和描述。 如需有關程式碼和可能解決方案的詳細資訊，請參閱[錯誤參考](container-registry-health-error-reference.md)。

根據預設，命令會在發現錯誤時停止。 您也可以執行命令，讓它提供所有健康情況檢查的輸出，即使找到錯誤也一樣。 新增 `--ignore-errors` 參數，如下列範例所示：

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

範例輸出：

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>後續步驟

如需[az acr check-health][az-acr-check-health]命令所傳回之錯誤碼的詳細資訊，請參閱[健康情況檢查錯誤參考](container-registry-health-error-reference.md)。

如需有關 Azure Container Registry 的常見問題和其他已知問題，請參閱[常見問題](container-registry-faq.md)。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
