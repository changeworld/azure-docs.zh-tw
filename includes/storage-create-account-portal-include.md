---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bed8475e5d6c7bf26003672b6cf9ce51a82384ad
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376975"
---
若要在 Azure 入口網站中建立一般用途 v2 儲存體帳戶，請遵循下列步驟：

1. 在 Azure 入口網站功能表上，選取 [所有服務]  。 在資源清單中，輸入**儲存體帳戶**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [儲存體帳戶]  。
1. 在出現的 [儲存體帳戶]  視窗上，選擇 [新增]  。
1. 在 [基本] 索引標籤上，選取要在其中建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組]  欄位下，選取您想要的資源群組，或建立新的資源群組。  如需 Azure 資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../articles/azure-resource-manager/resource-group-overview.md)。
1. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
1. 選取儲存體帳戶的位置，或使用預設位置。
1. 選取一個效能層級。 預設階層為 [標準]。
1. 將 [帳戶類型] 欄位設定為 [Storage V2 (一般用途 v2)]。
1. 指定儲存體帳戶的複寫方式。 預設複寫選項為「讀取權限異地備援儲存體 (RA-GRS)」。 如需可用複寫選項的詳細資訊，請參閱 [Azure 儲存體備援](../articles/storage/common/storage-redundancy.md)。
1. 在儲存體帳戶中指定 blob 的存取層。 預設階層為 [經常性存取層]。 如需 blob 存取層的詳細資訊，請參閱 [Blob 的經常性存取層、非經常性存取層，以及封存存取層](../articles/storage/blobs/storage-blob-storage-tiers.md)。
1. 若要使用 Azure Data Lake Storage，請選擇 [進階] 索引標籤，然後將 [階層命名空間] 設定為 [啟用]。 如需詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../articles/storage/blobs/data-lake-storage-introduction.md)
1. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。
1. 選取 [建立]。

下圖顯示新儲存體帳戶 [基本] 索引標籤上的設定：

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="螢幕擷取畫面，顯示如何在 Azure 入口網站中建立儲存體帳戶":::
