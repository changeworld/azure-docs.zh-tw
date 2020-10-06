---
title: Azure Stack Edge Pro GPU 儲存體帳戶管理 |Microsoft Docs
description: 說明如何使用 Azure 入口網站管理 Azure Stack Edge Pro 上的儲存體帳戶。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d3378b08ace73b2bc9a10286abf5a48f3063ba7f
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743500"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>使用 Azure 入口網站來管理 Azure Stack Edge Pro 上的 Edge 儲存體帳戶

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明如何在您的 Azure Stack Edge Pro 上管理 Edge 儲存體帳戶。 您可以透過 Azure 入口網站或透過本機 web UI 來管理 Azure Stack Edge Pro。 使用 Azure 入口網站，在您的裝置上新增或刪除 Edge 儲存體帳戶。

## <a name="about-edge-storage-accounts"></a>關於 Edge 儲存體帳戶

您可以透過 SMB、NFS 或 REST 通訊協定，從 Azure Stack Edge Pro 裝置傳輸資料。 若要使用 REST Api 將資料傳輸至 Blob 儲存體，您需要在 Azure Stack Edge Pro 上建立 Edge 儲存體帳戶。 

您在 Azure Stack Edge Pro 裝置上新增的 Edge 儲存體帳戶會對應至 Azure 儲存體帳戶。 寫入邊緣儲存體帳戶的任何資料都會自動推送至雲端。

詳細說明這兩種類型的帳戶，以及資料如何從這些帳戶流至 Azure 的圖表如下所示：

![Blob 儲存體帳戶的圖表](media/azure-stack-edge-j-series-manage-storage-accounts/ase-blob-storage.svg)

在本文中，您將學會如何：

> [!div class="checklist"]
> * 新增 Edge 儲存體帳戶
> * 刪除 Edge 儲存體帳戶


## <a name="add-an-edge-storage-account"></a>新增 Edge 儲存體帳戶

若要建立 Edge 儲存體帳戶，請執行下列程序：

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>刪除 Edge 儲存體帳戶

請採取下列步驟來刪除 Edge 儲存體帳戶。

1. 移至您資源中的 [ **Configuration > 儲存體帳戶** ]。 從儲存體帳戶清單中，選取您要刪除的儲存體帳戶。 從頂端的命令列中，選取 [ **刪除儲存體帳戶**]。

    ![移至儲存體帳戶清單](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-1.png)

2. 在 [ **刪除儲存體帳戶** ] 分頁中，確認要刪除的儲存體帳戶，然後選取 [ **刪除**]。

    ![確認並刪除儲存體帳戶](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-2.png)

儲存體帳戶清單會更新，以反映刪除。


## <a name="add-delete-a-container"></a>新增、刪除容器

您也可以新增或刪除這些儲存體帳戶的容器。

若要加入容器，請執行下列步驟：

1. 選取您要管理的儲存體帳戶。 從頂端的命令列中，選取 [ **+ 新增容器**]。

    ![選取要新增容器的儲存體帳戶](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-1.png)

2. 提供容器的名稱。 此容器會建立在您的 Edge 儲存體帳戶，以及對應至此帳戶的 Azure 儲存體帳戶中。 

    ![新增 Edge 容器](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-2.png)

容器清單會更新，以反映新加入的容器。

![更新的容器清單](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-4.png)

您現在可以從此清單中選取容器，然後從頂端命令列選取 [ **+ 刪除容器** ]。 

![刪除容器](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>同步儲存體金鑰

您可以同步處理裝置上 Edge (本機) 儲存體帳戶的存取金鑰。 

若要同步儲存體帳戶存取金鑰，請執行下列步驟：

1. 在您的資源中，選取您要管理的儲存體帳戶。 從頂端的命令列中，選取 [ **同步處理儲存體金鑰**]。

    ![選取同步儲存體金鑰](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-1.png)

2. 系統提示您確認時，請選取 [是]****。

    ![選取同步儲存體金鑰2](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>後續步驟

- 了解如何[透過 Azure 入口網站管理使用者](azure-stack-edge-j-series-manage-users.md)。
