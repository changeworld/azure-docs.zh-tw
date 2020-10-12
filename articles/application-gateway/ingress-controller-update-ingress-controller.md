---
title: 使用 Helm 升級輸入控制器
description: 本文提供有關如何使用 Helm 升級應用程式閘道輸入的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84806769"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>如何使用 Helm 來升級應用程式閘道輸入控制器 

Kubernetes (AGIC) 的 Azure 應用程式閘道輸入控制器，可以使用 Azure 儲存體上裝載的 Helm 存放庫進行升級。

在開始進行升級程式之前，請確定您已新增必要的存放庫：

- 使用下列內容來查看您目前新增的 Helm 存放庫：

    ```bash
    helm repo list
    ```

- 使用下列內容新增 AGIC 存放庫：

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>升級

1. 重新整理 AGIC Helm 存放庫以取得最新版本：

    ```bash
    helm repo update
    ```

1. 查看圖表的可用版本 `application-gateway-kubernetes-ingress` ：

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    範例回應：

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    上述清單中的最新可用版本為： `0.7.0-rc1`

1. 查看目前已安裝的 Helm 圖：

    ```bash
    helm list
    ```

    範例回應：

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    上述範例回應中的 Helm 圖表安裝名為 `odd-billygoat` 。 我們會將此名稱用於其餘的命令。 您實際的部署名稱很有可能不同。

1. 將 Helm 部署升級至新版本：

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>復原

如果 Helm 部署失敗，您可以回復為先前的版本。

1. 取得最後一個已知良好的版本號碼：

    ```bash
    helm history odd-billygoat
    ```

    範例輸出：

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    從命令的範例輸出中， `helm history` 它看起來就像是最後一個成功部署的 `odd-billygoat` was 修訂 `1`

1. 復原到上一個成功的修訂：

    ```bash
    helm rollback odd-billygoat 1
    ```
