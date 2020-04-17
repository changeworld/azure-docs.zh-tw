---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537964"
---
# <a name="create-an-azure-hpc-cache"></a>建立 Azure HPC Cache

使用 Azure 入口網站建立快取。

![Azure 入口網站中快取概觀的螢幕擷取畫面，其中 [建立] 按鈕位於底部](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>定義基本詳細資料

![Azure 入口網站中專案詳細資料頁面的螢幕擷取畫面](media/hpc-cache-create-basics.png)

在 [專案詳細資料]**** 中，選取將要裝載快取的訂用帳戶和資源群組。

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

## <a name="enable-azure-key-vault-encryption-optional"></a>開啟 Azure 金鑰保存庫加密(可選)

如果您的快取位於支援客戶管理的加密金鑰的區域,**則「磁碟加密金鑰」** 頁將顯示在 **「快取**」和 **「標記」** 選項卡之間。 截至發佈時,此選項在美國東部、美國中南部和美國西部2中支援。

如果要管理與快取儲存一起使用的加密密鑰,請在 **「磁碟加密金鑰」** 頁上提供 Azure 金鑰保管庫資訊。 密鑰保管庫必須位於與緩存相同的區域和訂閱中。

如果您不需要客戶管理的金鑰,則可以跳過此部分。 默認情況下,Azure 使用Microsoft管理的密鑰加密數據。 閱讀[Azure 儲存加密](../storage/common/storage-service-encryption.md)以瞭解更多資訊。

> [!NOTE]
>
> * 建立快取後,無法在 Microsoft 管理的金鑰和客戶管理的密鑰之間進行更改。
> * 建立快取後,必須授權它存取金鑰保管庫。 按下快取 **「概述」** 頁中的 **「啟用加密**」按鈕以打開加密。 在創建緩存后的 90 分鐘內執行此步驟。
> * 緩存磁碟是在此授權後創建的。 這意味著初始快取創建時間較短,但在您授權訪問後,緩存將無法使用 10 分鐘或更長時間。

關於客戶管理金鑰加密過程的完整說明,請改為[使用 Azure HPC 快取的客戶託管加密金鑰](customer-keys.md)。

![加密金鑰頁的螢幕截圖,其中選擇了「客戶託管」,並顯示金鑰保管庫欄位](media/create-encryption.png)

選擇**客戶管理以**選擇客戶管理金鑰加密。 將顯示金鑰保管庫規範欄位。 選擇要使用的 Azure 金鑰保管庫,然後選擇要用於此快取的金鑰和版本。 金鑰必須為 2048 位 RSA 密鑰。 可以從此頁面創建新的金鑰保管庫、金鑰或密鑰版本。

建立快取後,必須授權它使用金鑰保管庫服務。 請從[快取中讀取授權 Azure 金鑰保管庫加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache),瞭解詳細資訊。

## <a name="add-resource-tags-optional"></a>新增資源標記 (選擇性)

[標籤]**** 頁面可讓您將[資源標籤](https://go.microsoft.com/fwlink/?linkid=873112)新增至 Azure HPC Cache 執行個體。

## <a name="finish-creating-the-cache"></a>完成建立快取

設定新快取後,按下「審閱 **+ 創建**」選項卡。門戶驗證您的選擇,並允許您查看您的選擇。 如果所有設定都正確，請按一下 [建立]****。

建立快取需要大約 10 分鐘。 您可以在 Azure 入口網站的通知面板中追蹤進度。

![入口網站中快取建立的「部署進行中」和「通知」頁面螢幕擷取畫面](media/hpc-cache-deploy-status.png)

建立完成後，會出現包含新 Azure HPC Cache 執行個體連結的通知，而快取會出現在訂用帳戶的**資源**清單中。

![Azure 入口網站中 Azure HPC Cache 執行個體的螢幕擷取畫面](media/hpc-cache-new-overview.png)

> [!NOTE]
> 如果快取使用客戶管理的加密金鑰,則在部署狀態更改完成之前,快取可能會顯示在資源清單中。 一旦快取的狀態等待**金鑰,** 您可以[授權它](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)使用金鑰保管庫。

## <a name="next-steps"></a>後續步驟

快取顯示在 **「資源」** 清單中後,可以移動到下一步。

* [定義存儲目標](hpc-cache-add-storage.md),以便授予快取對資料來源的存取權限。
* 如果使用客戶管理的加密密鑰,則需要從快取的概述頁[授權 Azure 金鑰保管庫加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)以完成緩存設置。 必須先執行此步驟,然後才能添加存儲。 閱讀[使用客戶管理的加密密鑰](customer-keys.md)瞭解詳細資訊。
