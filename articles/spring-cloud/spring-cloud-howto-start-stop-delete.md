---
title: 啟動、停止和刪除 Azure 春雲應用程式 |微軟文檔
description: 如何啟動、停止和刪除您的 Azure Spring Cloud 應用程式
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276829"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>啟動、停止和刪除 Azure 春雲應用程式

本指南介紹如何使用 Azure 門戶或 Azure CLI 更改應用程式在 Azure 春雲中的狀態。

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

部署應用程式後，可以使用 Azure 門戶啟動、停止和刪除應用程式。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務執行個體。
1. 選取 [應用程式儀表板]**** 索引標籤。
1. 選取您想要變更狀態的應用程式。
1. 在該應用程式的 **"概述"** 頁上，選擇 **"開始/停止**"、"**重新開機**"或 **"刪除**"。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 您可以搭配 Azure CLI 來使用選擇性參數及設定預設值。 通過閱讀[我們的參考文檔](spring-cloud-cli-reference.md)，瞭解有關 Azure CLI 的更多。  

首先，為 Azure CLI 安裝 Azure 春雲擴展，如下所示：

```azurecli
az extension add --name spring-cloud
```

接下來，選擇以下任一 Azure CLI 操作：

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
