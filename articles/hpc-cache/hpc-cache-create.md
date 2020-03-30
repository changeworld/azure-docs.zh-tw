---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129560"
---
# <a name="create-an-azure-hpc-cache"></a>建立 Azure HPC Cache

使用 Azure 入口網站建立快取。

![Azure 入口網站中快取概觀的螢幕擷取畫面，其中 [建立] 按鈕位於底部](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>定義基本詳細資料

![Azure 入口網站中專案詳細資料頁面的螢幕擷取畫面](media/hpc-cache-create-basics.png)

在 [專案詳細資料]**** 中，選取將要裝載快取的訂用帳戶和資源群組。 請確定訂用帳戶位於[存取](hpc-cache-prereqs.md#azure-subscription)清單上。

在 [服務詳細資料]**** 中，設定快取名稱和其他屬性：

* 位置 - 選取其中一個[支援的區域](hpc-cache-overview.md#region-availability)。
* 虛擬網路 - 您可以選取現有的虛擬網路，或建立一個新的虛擬網路。
* 子網路 - 選擇或建立子網路，必須至少有 64 個 IP 位址 (/24) 且只會用於此 Azure HPC Cache 執行個體。

## <a name="set-cache-capacity"></a>設定快取容量
<!-- referenced from GUI - update aka.ms link if you change this header text -->

在 [快取]**** 頁面上，您必須設定快取的容量。 此處設定的值會決定您的快取可以保存多少資料，以及它服務用戶端要求的速度。

容量也會影響快取的成本。

藉由設定這兩個值來選擇容量：

* 快取 (輸送量) 的資料傳輸率上限 (以 GB/秒為單位)
* 配置給快取資料的儲存體數量 (以 TB 為單位)

選擇其中一個可用的輸送量值和快取儲存體大小。

請記住，實際的資料傳輸速率取決於工作負載、網路速度，以及儲存體目標的類型。 您選擇的值會設定整個快取系統的最大輸送量，但其中有部分會用於額外負荷工作。 例如，如果用戶端要求的檔案尚未存放在快取中，或檔案被標示為過時，則您的快取會使用其部分的輸送量從後端儲存體提取該檔案。

Azure HPC Cache 會管理要快取和預先載入哪些檔案，以最大化快取命中率。 系統會持續評估快取內容，並將較不常存取的檔案移至長期儲存體。 選擇可輕鬆保存使用中檔案集的快取儲存體大小，且有額外空間可容納中繼資料和其他負荷。

![快取大小頁面的螢幕擷取畫面](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>新增資源標記 (選擇性)

[標籤]**** 頁面可讓您將[資源標籤](https://go.microsoft.com/fwlink/?linkid=873112)新增至 Azure HPC Cache 執行個體。

## <a name="finish-creating-the-cache"></a>完成建立快取

配置新緩存後，按一下"審閱 **+ 創建**"選項卡。門戶驗證您的選擇，並允許您查看您的選擇。 如果所有設定都正確，請按一下 [建立]****。

建立快取需要大約 10 分鐘。 您可以在 Azure 入口網站的通知面板中追蹤進度。

![入口網站中快取建立的「部署進行中」和「通知」頁面螢幕擷取畫面](media/hpc-cache-deploy-status.png)

建立完成後，會出現包含新 Azure HPC Cache 執行個體連結的通知，而快取會出現在訂用帳戶的**資源**清單中。
<!-- double check on notification -->

![Azure 入口網站中 Azure HPC Cache 執行個體的螢幕擷取畫面](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>後續步驟

當您的快取出現在**資源**清單中之後，請定義儲存體目標，讓快取能夠存取您的資料來源。

* [新增儲存體目標](hpc-cache-add-storage.md)
