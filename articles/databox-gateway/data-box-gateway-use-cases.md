---
title: Microsoft Azure 資料箱閘道使用案例 |Microsoft Docs
description: 說明 Azure 資料箱閘道的使用案例，這是可讓您將資料傳輸至 Azure 的虛擬裝置儲存體解決方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786607"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Azure Data Box Gateway 的使用案例

Azure Data Box Gateway 是您內部部署的雲端儲存空間閘道裝置，可將您的影像、媒體和其他資料傳送至 Azure。 此雲端儲存空間閘道是佈建在您 Hypervisor 中的虛擬機器。 您會使用 NFS 和 SMB 通訊協定將資料寫入此虛擬裝置，然後由裝置將資料傳送至 Azure。 本文將詳細說明可部署此裝置的案例。

針對下列案例使用 Data Box Gateway：

- 持續內嵌大量資料。
- 以安全且有效率的方式在雲端封存資料。
- 在使用資料箱完成初始大量資料傳輸後，透過網路進行增量資料傳輸。

下列各節將詳細說明這些案例。


## <a name="continuous-data-ingestion"></a>持續擷取資料

Data Box Gateway 的主要優點之一是能夠持續將資料嵌入裝置，然後複製到雲端，而且不論資料大小為何。

資料寫入至閘道裝置後，裝置就會將資料上傳至 Azure 儲存體。 裝置會在到達特定閾值時，從本機刪除檔案但保留中繼資料，藉此來自動管理儲存體。 保存中繼資料的本機複本，可讓閘道裝置在檔案更新時只上傳變更的部分。 上傳至閘道裝置的資料應遵循[資料上傳注意事項](data-box-gateway-limits.md#data-upload-caveats)中的指引。

當裝置裝滿資料時，就會開始對輸入速率進行節流 (視需要)，以符合資料上傳至雲端的速率。 若要監視裝置上的持續擷取，您可以使用警示。 這些警示會在節流開始時引發，並在節流停止時清除。

## <a name="cloud-archival-of-data"></a>在雲端封存資料

當您想要將資料長期保留在雲端時，即可使用 Data Box Gateway。 您可以使用封存儲存層來進行長期保留。

封存層已優化，可將很少存取的資料儲存至少180天。 封存層提供最低的儲存成本，但存取成本最高。 如需詳細資訊，請移至[封存存取層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)。

### <a name="move-data-to-the-archive-tier"></a>將資料移至封存層

在開始之前，請確定您有執行中的 Data Box Gateway 裝置。 遵循 [教學課程：準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md) 並繼續進行下一個教學課程中所述的步驟，直到您有操作裝置為止。

- 透過[使用 Data Box Gateway 傳輸資料](data-box-gateway-deploy-add-shares.md)中所述的一般傳輸程序，使用 Data Box Gateway 裝置將資料上傳至 Azure。
- 將資料上傳之後，您必須將資料移至封存層。 您可以使用兩種方式來設定 blob 層：使用 Azure PowerShell 腳本或 Azure 儲存體生命週期管理原則。  
    - 如果使用 Azure PowerShell，請遵循下列 [步驟](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) ，將資料移至封存層。
    - 如果使用 Azure 生命週期管理，請遵循下列步驟，將資料移至封存層。
        - [註冊](../storage/blobs/storage-lifecycle-management-concepts.md) Blob 生命週期管理服務的預覽，以使用封存層。
        - 使用下列原則來[封存內嵌資料](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest)。
- 一旦 blob 標示為封存，閘道就無法再修改它們，除非這些 blob 移至經常性存取層或非經常性存取層。 如果檔案在本機儲存體中，對本機複本所做的任何變更 (包括刪除) 都不會上傳到封存層。
- 若要讀取封存儲存體中的資料，您必須將 blob 層變更為經常性存取或非經常性存取，以解除凍結資料。 在閘道上[重新整理共用](data-box-gateway-manage-shares.md#refresh-shares)不會將 Blob 解除凍結。

如需詳細資訊，請了解如何[管理 Azure Blob 儲存體生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)。

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>初始大量傳輸後接著增量傳輸

如果您想要在上傳大量資料後，接著進行增量傳輸，您可以搭配使用資料箱和 Data Box Gateway。 以離線模式使用資料箱來進行大量傳輸 (初始植入)，然後使用 Data Box Gateway 透過網路進行增量傳輸 (持續饋送)。

### <a name="seed-the-data-with-data-box"></a>使用資料箱植入資料

請遵循下列步驟來將資料複製到資料箱，並上傳至 Azure 儲存體。

1. [訂購資料箱](../databox/data-box-deploy-ordered.md)。
2. [設定資料箱](../databox/data-box-deploy-set-up.md)。
3. [透過 SMB 將資料複製到資料箱](../databox/data-box-deploy-copy-data.md)。
4. [返回資料箱，確認資料已上傳至 Azure](../databox/data-box-deploy-picked-up.md)。
5. 將資料上傳至 Azure 之後，所有資料應該會位在 Azure 儲存體容器中。 在資料箱的儲存體帳戶中，移至 Blob (和檔案) 容器，確定已複製所有資料。 請記下容器名稱，因為您稍後將使用此名稱。 例如，在下列螢幕擷取畫面中，`databox` 容器將用於增量傳輸。

    ![資料箱上有資料的容器](media/data-box-gateway-use-cases/data-container.png)

此大量傳輸會完成初始植入階段。

### <a name="ongoing-feed-with-data-box-gateway"></a>使用 Data Box Gateway 進行持續饋送

請依照下列步驟使用 Data Box Gateway 進行持續擷取。 

1. 在 Data Box Gateway 上建立雲端共用。 此共用會自動將任何資料上傳至 Azure 儲存體帳戶。 移至 Data Box Gateway 資源中的 [共用]，然後按一下 [+ 新增共用]。

    ![按一下 [新增共用]](media/data-box-gateway-use-cases/add-share.png)

2. 請確定此共用會對應到包含植入資料的容器。 針對 [選取 Blob 容器]，請選擇 [使用現有項目]，並瀏覽至其中有資料箱所傳輸資料的容器。

    ![共用設定](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. 建立共用之後，請重新整理共用。 這項作業會重新整理內部部署共用與 Azure 的內容。

    ![重新整理共用](media/data-box-gateway-use-cases/refresh-share.png)

    若同步共用，則每當用戶端上有檔案修改時，Data Box Gateway 就會上傳累加的變更。

## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱閘道的系統需求](data-box-gateway-system-requirements.md)。
- 了解[資料箱閘道的限制](data-box-gateway-limits.md)。
- 在 Azure 入口網站中部署 [Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。