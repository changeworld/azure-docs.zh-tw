---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 894595ee3660532bf046a39e994fa669f7c6b002
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84434087"
---
# <a name="create-an-azure-hpc-cache"></a>建立 Azure HPC Cache

使用 Azure 入口網站建立快取。

![Azure 入口網站中快取概觀的螢幕擷取畫面，其中 [建立] 按鈕位於底部](media/hpc-cache-home-page.png)

按一下下方影像，觀看示範如何建立快取和新增儲存體目標的[影片](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)。

[![影片縮圖： Azure HPC 快取：安裝（按一下以流覽影片頁面）](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="define-basic-details"></a>定義基本詳細資料

![Azure 入口網站中專案詳細資料頁面的螢幕擷取畫面](media/hpc-cache-create-basics.png)

在 [專案詳細資料]**** 中，選取將要裝載快取的訂用帳戶和資源群組。 請確定訂用帳戶位於[存取](hpc-cache-prereqs.md#azure-subscription)清單上。

在 [服務詳細資料]**** 中，設定快取名稱和其他屬性：

* 位置 - 選取其中一個[支援的區域](hpc-cache-overview.md#region-availability)。
* 虛擬網路 - 您可以選取現有的虛擬網路，或建立一個新的虛擬網路。
* 子網-選擇或建立至少有64個 IP 位址（/24）的子網。 此子網必須僅用於此 Azure HPC 快取實例。

## <a name="set-cache-capacity"></a>設定快取容量
<!-- referenced from GUI - update aka.ms link if you change this header text -->

在 [快取]**** 頁面上，您必須設定快取的容量。 此處設定的值會決定您的快取可以保存多少資料，以及它服務用戶端要求的速度。

容量也會影響快取的成本。

藉由設定這兩個值來選擇容量：

* 快取 (輸送量) 的資料傳輸率上限 (以 GB/秒為單位)
* 配置給快取資料的儲存體數量 (以 TB 為單位)

選擇其中一個可用的輸送量值和快取儲存體大小。

請記住，實際的資料傳輸速率取決於工作負載、網路速度，以及儲存體目標的類型。 您選擇的值會設定整個快取系統的最大輸送量，但其中有部分會用於額外負荷工作。 例如，如果用戶端要求的檔案尚未儲存在快取中，或檔案被標示為過時，則您的快取會使用其部分輸送量從後端儲存體提取該檔案。

Azure HPC Cache 會管理要快取和預先載入哪些檔案，以最大化快取命中率。 快取內容會持續進行評估，而當檔案較不常存取時，檔案就會移至長期儲存。 選擇可輕鬆保存作用中檔案集的快取儲存體大小，以及中繼資料和其他額外負荷的額外空間。

![快取大小頁面的螢幕擷取畫面](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>啟用 Azure Key Vault 加密（選擇性）

如果您的快取位於支援客戶管理的加密金鑰的區域中，[**磁片加密金鑰**] 頁面會**出現在 [** 快取] 和 [**標記**] 索引標籤之間。 在發行時，美國東部、美國中南部和美國西部2都支援此選項。

如果您想要管理用於快取儲存體的加密金鑰，請在 [**磁片加密金鑰**] 頁面上提供您的 Azure Key Vault 資訊。 金鑰保存庫必須位於與快取相同的區域和相同的訂用帳戶中。

如果您不需要客戶管理的金鑰，您可以略過本節。 Azure 預設會使用 Microsoft 管理的金鑰來加密資料。 若要深入瞭解，請參閱[Azure 儲存體加密](../storage/common/storage-service-encryption.md)。

> [!NOTE]
>
> * 建立快取之後，您就無法在 Microsoft 管理的金鑰與客戶管理的金鑰之間變更。
> * 建立快取之後，您必須授權它存取金鑰保存庫。 按一下快取的 **[總覽**] 頁面中的 [**啟用加密**] 按鈕，以開啟加密。 在建立快取的90分鐘內進行此步驟。
> * 在此授權之後，會建立快取磁片。 這表示初始快取建立時間很短，但在您授權存取之後，快取將不會準備好使用十分鐘或更久。

如需客戶管理的金鑰加密程式的完整說明，請參閱[針對 AZURE HPC 快取使用客戶管理的加密金鑰](customer-keys.md)。

![[加密金鑰] 頁面的螢幕擷取畫面，其中已選取 [客戶管理]，並顯示 key vault 欄位](media/create-encryption.png)

選取 [**客戶管理**] 以選擇客戶管理的金鑰加密。 [金鑰保存庫規格] 欄位隨即出現。 選取要使用的 Azure Key Vault，然後選取要用於此快取的金鑰和版本。 金鑰必須是2048位的 RSA 金鑰。 您可以從這個頁面建立新的金鑰保存庫、金鑰或金鑰版本。

建立快取之後，您必須授權它使用金鑰保存庫服務。 如需詳細資訊，請參閱從快取[授權 Azure Key Vault 加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)。

## <a name="add-resource-tags-optional"></a>新增資源標記 (選擇性)

[標籤]**** 頁面可讓您將[資源標籤](https://go.microsoft.com/fwlink/?linkid=873112)新增至 Azure HPC Cache 執行個體。

## <a name="finish-creating-the-cache"></a>完成建立快取

設定新的快取之後，請按一下 [**審查 + 建立**] 索引標籤。入口網站會驗證您的選擇，並讓您查看您的選擇。 如果所有設定都正確，請按一下 [建立]****。

建立快取需要大約 10 分鐘。 您可以在 Azure 入口網站的通知面板中追蹤進度。

![入口網站中快取建立的「部署進行中」和「通知」頁面螢幕擷取畫面](media/hpc-cache-deploy-status.png)

建立完成後，會出現包含新 Azure HPC Cache 執行個體連結的通知，而快取會出現在訂用帳戶的**資源**清單中。

![Azure 入口網站中 Azure HPC Cache 執行個體的螢幕擷取畫面](media/hpc-cache-new-overview.png)

> [!NOTE]
> 如果您的快取使用客戶管理的加密金鑰，則快取可能會出現在 [資源] 清單中，且部署狀態會變更為 [完成]。 一旦快取的狀態為 [**正在等候金鑰**]，您就可以[授權它](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)使用金鑰保存庫。

## <a name="next-steps"></a>後續步驟

當您的快取出現在**資源清單**之後，您就可以移至下一個步驟。

* [定義儲存體目標](hpc-cache-add-storage.md)，為您的資料來源提供快取存取權。
* 如果您使用客戶管理的加密金鑰，您必須從快取的 [總覽] 頁面[授權 Azure Key Vault 加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)，才能完成您的快取設定。 您必須先執行此步驟，才能新增儲存體。 如需詳細資訊，[請參閱使用客戶管理的加密金鑰](customer-keys.md)。
