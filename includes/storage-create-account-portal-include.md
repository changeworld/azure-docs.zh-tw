---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76759838"
---
若要在 Azure 入口網站中建立一般用途 v2 儲存體帳戶，請遵循下列步驟：

1. 在 Azure 入口網站功能表上，選取 [所有服務]  。 在資源清單中，輸入**儲存體帳戶**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [儲存體帳戶]  。
2. 在出現的 [儲存體帳戶]  視窗上，選擇 [新增]  。
3. 選取要在其中建立儲存體帳戶的訂用帳戶。
4. 在 [資源群組]  欄位下方，選取 [新建]  。 輸入新資源群組的名稱，如下圖所示。

    ![示範如何在入口網站中建立資源群組的螢幕擷取畫面](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. 接下來，輸入儲存體帳戶的名稱。 您所選擇的名稱在整個 Azure 中必須是唯一的。 名稱的長度必須介於 3 到 24 個字元之間，且只能包含數字和小寫字母。
6. 選取儲存體帳戶的位置，或使用預設位置。
7. 讓這些欄位設定為其預設值：

   |欄位  |值  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |效能     |標準         |
   |帳戶類型     |StorageV2 (一般用途 v2)         |
   |複寫     |讀取權限異地備援儲存體 (RA-GRS)         |
   |存取層     |經常性存取         |

8. 如果您打算使用 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)，請選擇 [進階]  索引標籤，然後將 [階層命名空間]  設定為 [啟用]  。
9. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。
10. 選取 [建立]  。

如需儲存體帳戶類型及其他儲存體帳戶設定的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 
