---
title: 啟動、停止及刪除您的 Azure 春季雲端應用程式 |Microsoft Docs
description: 如何啟動、停止和刪除您的 Azure Spring Cloud 應用程式
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c8d5b983e376243eca83b929f87ff1e44d4b3470
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500363"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>啟動、停止及刪除您的 Azure 春季雲端應用程式

本指南說明如何使用 Azure 入口網站或 Azure CLI，在 Azure 春季雲端中變更應用程式的狀態。

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

部署應用程式之後，您可以使用 Azure 入口網站來啟動、停止和刪除它。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務執行個體。
1. 選取 [應用程式儀表板]**** 索引標籤。
1. 選取您想要變更狀態的應用程式。
1. 在該應用程式的 [總覽] 頁面上，選取 [**啟動/停止**]、[**重新開機**] 或 **[** **刪除**]。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 您可以搭配 Azure CLI 來使用選擇性參數及設定預設值。 閱讀[我們的參考檔](spring-cloud-cli-reference.md)，以深入瞭解 Azure CLI。  

首先，安裝適用于 Azure CLI 的 Azure 春季雲端延伸模組，如下所示：

```azurecli
az extension add --name spring-cloud
```

接下來，選取下列其中一項 Azure CLI 作業：

* 啟動您的應用程式：

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 停止您的應用程式：

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 重新啟動您的應用程式：

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 刪除您的應用程式：

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
