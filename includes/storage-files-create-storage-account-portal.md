---
title: storage-files-create-storage-account-portal
description: 建立 Azure 檔案服務的儲存體帳戶。
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03cf20e5c796a7092dc16c466934f377c945ad48
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509252"
---
儲存體帳戶是您可在其中部署 Azure 檔案共用或其他儲存體資源 (例如 blob 或佇列) 的共用儲存體集區。 儲存體帳戶可以包含無限多個共用。 共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。

建立儲存體帳戶：

1. 在左功能表中選取 **+** 以建立資源。
1. 在搜尋方塊中輸入 **儲存體帳戶** 並選取 [儲存體帳戶 - Blob、檔案、資料表、佇列]，然後選取 [建立]。
    ![資源搜尋對話方塊中儲存體帳戶項目外觀的螢幕擷取畫面](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. 在 [名稱] 中，輸入 mystorageacct 且後面加上幾個隨機數字，直到您看見綠色核取記號，表示它是唯一的名稱為止。 儲存體帳戶名稱必須全部小寫，並且是全域唯一的。 請記下您的儲存體帳戶名稱。 稍後您將會用到此資訊。 
1. 在 [部署模型] 中，保留 [Resource Manager] 的預設值。 若要深入了解 Azure Resource Manager 與傳統部署模型之間的詳細資訊，請參閱[了解部署模型和資源的狀態](../articles/azure-resource-manager/management/deployment-models.md)。
1. 在 [效能] 中，保留 [標準] 的預設值。
    
    > [!NOTE]
    > 本快速入門會建立標準檔案共用，但如果您想使用進階檔案共用，請改為選取 [進階]。

1. 在 [帳戶種類] 中選取 [StorageV2]。 若要深入了解不同種類的儲存體帳戶，請參閱[了解 Azure 儲存體帳戶](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

    > [!NOTE]
    > 本快速入門會建立一般用途的 v2 帳戶。 如果您想使用進階檔案共用，請改為選取 **FileStorage**。

1. 在 [複寫] 中，選取 [本地備援儲存體 (LRS)]。 
1. 在 [需要安全傳輸] 中，我們建議一律選取 [已啟用]。 若要深入了解這個選項，請參閱[了解傳輸中加密](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
1. 在 [訂用帳戶] 中，選取用來建立儲存體帳戶的訂用帳戶。 如果您只有一個訂用帳戶，它應該是預設訂用帳戶。
1. 在 [資源群組] 中，選取 [新建]。 針對名稱輸入 myResourceGroup。
1. 在 [位置] 中，選取 [美國東部]。
1. 在 [虛擬網路] 中，將預設選項保留為 [已停用]。 
1. 選取 [釘選到儀表板]，可讓您更輕鬆地尋找儲存體帳戶。
1. 當您完成時，選取 [建立] 以開始部署。