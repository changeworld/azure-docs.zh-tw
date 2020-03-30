---
title: 檢查註冊表運行狀況
description: 瞭解如何運行快速診斷命令，以確定使用 Azure 容器註冊表時的常見問題，包括本地 Docker 配置和與註冊表的連接
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456404"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>檢查 Azure 容器註冊表的運行狀況

使用 Azure 容器註冊表時，偶爾會遇到問題。 例如，由於本地環境中的 Docker 出現問題，可能無法拉取容器映射。 或者，網路問題可能會阻止您連接到註冊表。 

作為第一個診斷步驟，運行[az acr 檢查運行狀況][az-acr-check-health]命令，以獲取有關環境運行狀況的資訊，並可以選擇訪問目標注冊表。 此命令在 Azure CLI 版本 2.0.67 或更高版本中可用。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="run-az-acr-check-health"></a>運行 az acr 檢查運行狀況

以下示例顯示了運行`az acr check-health`命令的不同方法。

> [!NOTE]
> 如果在 Azure 雲殼中運行該命令，則不會檢查本地環境。 但是，您可以檢查對目標注冊表的訪問。

### <a name="check-the-environment-only"></a>僅檢查環境

要檢查本地 Docker 守護進程、CLI 版本和 Helm 用戶端配置，請運行該命令而不執行其他參數：

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>檢查環境和目標注冊表

要檢查對註冊表的訪問以及執行本地環境檢查，請傳遞目標注冊表的名稱。 例如：

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>錯誤報告

該命令將資訊記錄到標準輸出。 如果檢測到問題，則提供錯誤代碼和說明。 有關代碼和可能的解決方案的詳細資訊，請參閱[錯誤引用](container-registry-health-error-reference.md)。

預設情況下，每當發現錯誤時，命令都會停止。 您還可以運行該命令，以便它為所有運行狀況檢查提供輸出，即使發現錯誤也是如此。 添加`--ignore-errors`參數，如以下示例所示：

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

有關[az acr 檢查運行狀況][az-acr-check-health]命令返回的錯誤代碼的詳細資訊，請參閱[運行狀況檢查錯誤引用](container-registry-health-error-reference.md)。

有關 Azure 容器註冊表的常見問題和其他已知問題，請參閱[常見問題解答](container-registry-faq.md)。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
