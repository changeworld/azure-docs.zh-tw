---
title: 使用 Helm 升級入口控制器
description: 本文提供有關如何使用 Helm 升級應用程式閘道入口的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795895"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>如何使用 Helm 來升級應用程式閘道輸入控制器 

庫伯內斯 （AGIC） 的 Azure 應用程式閘道入口控制器可以使用託管在 Azure 存儲上的 Helm 存儲庫進行升級。

在開始升級過程之前，請確保您已添加所需的存儲庫：

- 查看當前添加的 Helm 存儲庫：：

    ```bash
    helm repo list
    ```

- 添加 AGIC 回購：

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>升級

1. 刷新 AGIC 頭盔存儲庫以獲取最新版本：

    ```bash
    helm repo update
    ```

1. 查看圖表的`application-gateway-kubernetes-ingress`可用版本：

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    範例回應：

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    上面清單中的最新可用版本是：`0.7.0-rc1`

1. 查看當前安裝的頭盔圖：

    ```bash
    helm list
    ```

    範例回應：

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    上面的示例回應中的 Helm 圖表安裝名為`odd-billygoat`。 我們將使用此名稱對其餘命令。 實際部署名稱很可能會有所不同。

1. 將 Helm 部署升級到新版本：

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>復原

如果 Helm 部署失敗，則可以回滾到以前的版本。

1. 獲取最後一個已知的健康版本號：

    ```bash
    helm history odd-billygoat
    ```

    範例輸出：

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    從`helm history`命令的示例輸出來看，我們上次成功部署`odd-billygoat`的修訂版看起來`1`

1. 回滾到上次成功修訂版：

    ```bash
    helm rollback odd-billygoat 1
    ```
