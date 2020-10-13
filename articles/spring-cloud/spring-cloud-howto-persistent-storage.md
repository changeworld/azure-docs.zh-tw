---
title: 如何在 Azure Spring Cloud 中使用持續性儲存體 | Microsoft Docs
description: 如何在 Azure Spring Cloud 中使用持續性儲存體
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9d769cb6e2cc33df259da46a82ad27a8bfd9e74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888532"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>在 Azure Spring Cloud 中使用持續性儲存體

**本文適用於：** ✔️ Java ✔️ C#

Azure 春季雲端為您的應用程式提供兩種類型的儲存體：持續性和暫存。

根據預設，Azure 春季雲端會提供每個應用程式實例的暫存儲存體。 暫存儲存體的每個實例只能有 5 GB 的預設掛接路徑/tmp。

> [!WARNING]
> 如果您重新開機應用程式實例，則會永久刪除相關聯的暫存儲存體。

持續性儲存體是 Azure 所管理的檔案共用容器，並會針對每個應用程式進行配置。 儲存在持續性儲存體中的資料會由應用程式的所有實例共用。 Azure 春季雲端實例最多可以有10個應用程式，並啟用持續性儲存體。 每個應用程式都會配置 50 GB 的持續性儲存體。 持續性儲存體的預設掛接路徑為/persistent。

> [!WARNING]
> 如果您停用應用程式的持續性儲存體，則會解除配置所有該儲存體，並遺失所有儲存的資料。

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>使用 Azure 入口網站來啟用持續性儲存體

1. 從 Azure 入口網站 **的 [首頁** ] 頁面，選取 [ **所有資源**]。

    >![找出所有資源圖示](media/portal-all-resources.jpg)

1. 選取需要持續性儲存體的 Azure 春季雲端資源。 在此範例中，選取的應用程式稱為 **upspring**。

    > ![選取您的應用程式](media/select-service.jpg)

1. 在 [ **設定** ] 標題下，選取 [ **應用程式**]。

1. 您的 Azure 春季雲端服務會出現在資料表中。  選取您要新增持續性儲存體的服務。 在此範例中，已選取 **閘道** 服務。

    > ![選取您的服務](media/select-gateway.jpg)

1. 從服務的 [設定] 頁面中 **，選取 [** 設定]

1. 選取 [ **持續性儲存體** ] 索引標籤，然後選取 [ **啟用**]。

    > ![啟用持續性儲存體](media/enable-persistent-storage.jpg)

啟用持續性儲存體之後，其大小和路徑會顯示在 [設定] 頁面上。

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>使用 Azure CLI 修改持續性儲存體

如有必要，請安裝 Azure CLI 的春季雲端延伸模組：

```azurecli
az extension add --name spring-cloud
```
其他作業：

* 若要建立已啟用持續性儲存的應用程式：

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 若要為現有的應用程式啟用持續性儲存體：

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 若要在現有的應用程式中停用持續性儲存體：

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > 如果您停用應用程式的持續性儲存體，則會解除配置所有該儲存體，並永久遺失所有儲存的資料。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [應用程式和服務配額](spring-cloud-quotas.md)。
* 瞭解如何 [手動調整您的應用程式](spring-cloud-tutorial-scale-manual.md)。
