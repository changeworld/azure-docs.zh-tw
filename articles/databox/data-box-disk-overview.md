---
title: Microsoft Azure 資料箱磁碟概觀 | 資料中的 Microsoft Docs
description: 說明 Azure 資料箱磁碟，這是可讓您將大量資料傳輸至 Azure 的雲端解決方案
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 8e9b1faf2521e0698052dd1cdd1253191ae6f8bc
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84187396"
---
# <a name="what-is-azure-data-box-disk"></a>什麼是 Azure 資料箱磁碟？

Microsoft Azure 資料箱磁碟解決方案可讓您以快速、實惠和可靠的方式，將數 TB 的內部部署資料傳送至 Azure。 安全的資料傳輸可透過寄送給您的 1 到 5 個固態硬碟 (SSD) 加速運作。 這些 8 TB 的加密磁碟會透過當地貨運公司，寄送至您的資料中心。

您可以透過 Azure 入口網站中的資料箱服務，快速設定、連接和歸還這些磁碟。 請將資料複製到磁碟，然後將磁碟送回給 Azure。 在 Azure 資料中心內，您的資料會透過快速的私人網路上傳連結從磁碟機自動上傳至雲端。

## <a name="use-cases"></a>使用案例

您可以在沒有網路連線或網路連線受限的情況下，使用資料箱磁碟傳輸數 TB 的資料。 資料移動可以是單次或定期的，或一開始是大量資料傳輸，然後繼之以定期傳輸。

- **單次移轉** - 有大量的內部部署資料移至 Azure 時。 例如，將資料從離線磁帶移至 Azure 非經常性存取儲存體中的封存資料。
- **增量傳輸** - 在使用資料箱磁碟 (種子) 完成初始大量傳輸後，繼之以透過網路的增量傳輸時。 例如，使用 Commvault 和資料箱磁碟將備份複本移至 Azure。 在此移轉之後，會使用網路將累加的資料複製到 Azure 儲存體。
- **定期上傳** - 定期產生大量資料而需要移至 Azure 時。 例如，在進行能源探勘時，鑽井平台和風車農場會產生視訊內容。

### <a name="ingestion-of-data-from-data-box"></a>擷取資料箱的資料

Azure 提供者和非 Azure 提供者，都可從 Azure 資料箱擷取資料。 提供從 Azure 資料箱擷取資料的 Azure 服務包括：

- **SharePoint Online** - 使用 Azure 資料箱及 SharePoint 移轉工具 (SPMT)，將檔案共用內容移轉至 SharePoint Online。 使用資料箱可移除 WAN 連結上的相依性，以傳輸資料。 如需詳細資訊，請參閱[使用 Azure Data Box Heavy 將檔案共用內容遷移至 SharePoint Online](data-box-heavy-migrate-spo.md)。

- **Azure 檔案同步** - 將檔案從您的資料箱複寫到 Azure 檔案共用，可讓您在 Azure 內集中檔案服務，同時仍維持本機存取資料的功能。 如需詳細資訊，請參閱[部署 Azure 檔案同步](../storage/files/storage-sync-files-deployment-guide.md)。

- **HDFS 存放區** - 使用資料箱，將資料從 Hadoop 叢集的內部部署 Hadoop 分散式檔案系統 (HDFS) 存放區，移轉至 Azure 儲存體。 如需詳細資訊，請參閱[從內部部署的 HDFS 存放區遷移至具有 Azure 資料箱的 Azure 儲存體](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)。

- **Azure 備份** - 可讓您透過離線機制，將大量的重要企業資料備份，移至 Azure 復原服務保存庫。 如需詳細資訊，請參閱 [Azure 備份概觀](../backup/backup-overview.md)。

您可以使用資料箱的資料，搭配許多非 Azure 服務提供者。 例如：

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** - 可讓您使用 Azure 資料箱將大量資料移轉至 Microsoft Azure。
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** - 可讓您將大量資料從 Hyper-V 機器備份及複寫至資料箱。

如需與資料箱整合的其他非 Azure 服務提供者清單，請參閱 [Azure 資料箱合作夥伴](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf)。

## <a name="the-workflow"></a>工作流程

典型的流程包含下列步驟：

1. **訂購** - 在 Azure 入口網站中建立訂單，提供送貨資訊，以及資料的目的地 Azure 儲存體帳戶。 如果有可用的磁碟，Azure 即會加密、準備並運送附有出貨追蹤識別碼的磁碟。

2. **接收** - 在磁碟送達後，請打開包裝，並將磁碟連線至要複製的資料存放所在的電腦。 將磁碟解除鎖定。

3. **複製資料** - 藉由拖放將資料複製到磁碟上。

4. **送回** - 備妥磁碟並將其送回至 Azure 資料中心。

5. **上傳** - 資料會自動從磁碟複製到 Azure。 磁碟會根據美國國家標準與技術局 (NIST) 的指導方針安全地清除。

這整個過程中，都會透過電子郵件通知您所有狀態的變更。 如需更多有關詳細流程的資訊，請移至[在 Azure 入口網站中部署資料箱磁碟](data-box-disk-quickstart-portal.md)。

## <a name="benefits"></a>優點

資料箱磁碟依設計可將大量資料移至 Azure，而不會影響到網路。 此解決方案有下列優點：

- **速度** - 資料箱磁碟可使用 USB 3.0 連線在一週以內將高達 35 TB 的資料移至 Azure。

- **容易使用** - 資料箱簡單易用的解決方案。

  - 磁碟會使用 USB 連線，幾乎不需要花時間設定。
  - 磁碟的板型規格精巧，可讓您輕鬆地操作。
  - 磁碟不需要外接電源。
  - 磁碟可用於資料中心伺服器、桌上型電腦或膝上型電腦。
  - 此解決方案使用 Azure 入口網站提供端對端的追蹤。

- **安全** - 資料箱磁碟具有適用於磁碟、資料和服務的內建安全保護機制。
  - 磁碟可防竄改，並且支援安全更新功能。
  - 磁碟上的資料會全程受到 AES 128 位元加密的保護。
  - 磁碟只能使用 Azure 入口網站中提供的金鑰來解除鎖定。
  - 此服務受到 Azure 安全功能的保護。
  - 在您的資料上傳至 Azure 後，磁碟即會根據 NIST 800-88r1 標準進行清理。  

如需詳細資訊，請移至 [Azure 資料箱磁碟安全性和資料保護](data-box-disk-security.md)。

## <a name="features-and-specifications"></a>功能和規格

| 規格                                          | 描述              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 2 磅。 每個資料箱。 資料箱中最多有 5 個磁碟                |
| 維度                                              | 磁碟 - 2.5 吋 SSD |
| 纜線                                                  | 每個磁碟有 1 條 USB 3.1 纜線|
| 每份訂單的儲存體容量                              | 40 TB (可用容量最高 35 TB)|
| 磁碟儲存體容量                                   | 8 TB (可用容量最高 7 TB)|
| 資料介面                                          | USB   |
| 安全性                                                | 使用 BitLocker 預先加密以及安全更新 <br> 受通行金鑰保護的磁碟 <br> 資料全程加密  |
| 資料傳輸速率                                      | 最高 430 MBps，視檔案大小而定      |
|管理性                                               | Azure 入口網站 |

## <a name="region-availability"></a>區域可用性

如需區域可用性的資訊，請移至[依區域提供的 Azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) \(英文\)。 資料箱磁碟也可部署在 Azure Government 雲端中。 如需詳細資訊，請參閱[何謂 Azure Government？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)。

## <a name="pricing"></a>定價

如需定價的相關資訊，請移至[定價頁面](https://azure.microsoft.com/pricing/details/databox/disk/)。

## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱磁碟需求](data-box-disk-system-requirements.md)。
- 了解[資料箱磁碟限制](data-box-disk-limits.md)。
- 在 Azure 入口網站中快速部署 [Azure 資料箱磁碟](data-box-disk-quickstart-portal.md)。
