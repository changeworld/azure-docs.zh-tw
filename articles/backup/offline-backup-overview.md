---
title: 離線備份的總覽
description: 深入瞭解離線備份的各種元件，包括以 Azure 資料箱為基礎的離線備份和 Azure 匯入/匯出的離線備份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027013"
---
# <a name="overview-of-offline-backup"></a>離線備份的總覽

本文提供離線備份的總覽。

Azure 的初始完整備份通常會在線上傳輸大量資料，而相較于後續只傳送增量變更的備份，則需要更多的網路頻寬。 某些地理位置中的遠端辦公室或資料中心不一定有足夠的網路頻寬。 因此，這些初始備份需要幾天的時間，而在這段期間，會持續使用針對內部部署資料中心內執行的應用程式所布建的相同網路。

Azure 備份支援「離線備份」，這可讓您在不使用網路頻寬的情況下，離線傳送初始備份資料。 它提供一個機制，可將備份資料複製到實體儲存體上，然後傳送至附近 Azure 資料中心並上傳至復原服務保存庫的裝置。 此程式可確保備份資料的健全轉移，而不需要使用任何網路頻寬。

## <a name="offline-backup-options"></a>離線備份選項

離線備份會根據存放裝置的擁有權，以兩種模式提供。

1. 以 Azure 資料箱為基礎的離線備份（預覽）
2. 以 Azure 匯入/匯出為基礎的離線備份

## <a name="azure-data-box-based-offline-backup-preview"></a>以 Azure 資料箱為基礎的離線備份（預覽）

此模式目前支援 MARS 代理程式（預覽）。 此選項會利用[Azure 資料箱服務](https://azure.microsoft.com/services/databox/)將 Microsoft 專屬、安全且可篡改的傳輸設備與 USB 連接器一起寄送到您的資料中心或遠端辦公室，而備份資料則直接寫入這些裝置上。 **此選項可節省購買您自己的 Azure 相容磁片和連接器，或將暫存儲存體布建為預備位置所需的工作。** 此外，Microsoft 會處理端對端轉移物流，您可以透過 Azure 入口網站來追蹤。 描述使用此選項移動備份資料的架構如下所示。

![Azure 備份資料箱架構](./media/offline-backup-overview/azure-backup-databox-architecture.png)

以下是架構的摘要：

1. Azure 備份直接將備份資料複製到這些預先設定的裝置。
2. 接著，您可以將這些裝置寄回 Azure 資料中心。
3. Azure 資料箱服務會將資料複製到客戶擁有的儲存體帳戶。
4. Azure 備份會自動將備份資料從儲存體帳戶複製到指定的復原服務保存庫，並排定增量線上備份。

若要使用以 Azure 資料箱為基礎的離線備份，請參閱[這篇文章](offline-backup-azure-data-box.md)。

## <a name="azure-importexport-based-offline-backup"></a>以 Azure 匯入/匯出為基礎的離線備份

Azure 備份伺服器（MABS）/DPM-A/MARS 代理程式支援此選項。 它會利用[azure 匯入/匯出服務](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)，可讓您使用自己的 Azure 相容磁片和連接器，將初始備份資料傳輸至 Azure。 這種方法需要布建臨時存放裝置，稱為「預備**位置**」和「預先建立的公用程式」的使用方式，以將備份資料格式化並複製到客戶擁有的磁片上。 描述使用此選項移動備份資料的架構如下所示：

![Azure 備份匯入/匯出架構](./media/offline-backup-overview/azure-backup-import-export.png)

以下是架構的摘要：

1. Azure 備份將備份資料寫入至預備位置，而不是透過網路傳送備份資料。
2. 預備位置中的資料會使用自訂公用程式寫入一或多個 SATA 磁片。
3. 作為準備工作的一部分，公用程式會建立 Azure 匯入作業。 SATA 磁片磁碟機會運送到最接近的 Azure 資料中心，並參考匯入工作來連接活動。
4. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
5. Azure 備份會將儲存體帳戶中的資料複製到「復原服務」保存庫，而且會排定增量備份。

若要搭配使用 Azure 匯入/匯出型離線備份與 MARS 代理程式，請參閱[這篇文章](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)。

若要使用相同的搭配 MABS/DPM-A，請參閱[這篇文章](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。

## <a name="offline-backup-support-summary"></a>離線備份支援摘要

下表比較這兩個可用的選項，讓您可以根據您的案例做出適當的選擇。

| **考量**                                            | **以 Azure 資料箱為基礎的離線備份**                     | **以 Azure 匯入/匯出為基礎的離線備份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 備份部署模型                              | MARS 代理程式（預覽）                                              | MARS 代理程式，Azure 備份伺服器（MABS），DPM-A                                           |
| 每一伺服器的備份資料上限（MARS）或每個保護群組（MABS、DPM-A） | [Azure 資料箱磁碟](https://docs.microsoft.com/azure/databox/data-box-disk-overview)-7.2 TB <br> [Azure 資料箱](https://docs.microsoft.com/azure/databox/data-box-overview)-80 TB       | 80 TB （每個最多10個磁片，每個為 8 TB）                          |
| 安全性（資料、裝置 & 服務）                           | [資料](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection)-AES-256 位加密 <br> [裝置](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection)-堅固的案例，用來複製資料的專屬認證型介面 <br> 由 Azure 安全性功能保護的[服務](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | 資料-BitLocker 已加密                                 |
| 暫時預備位置布建                     | 不需要                                                | 大於或等於估計的備份資料大小        |
| 支援的區域                                           | [Azure 資料箱磁碟區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure 資料箱區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 匯入/匯出區域](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 跨國家/地區寄送 *                                     | **不受支援**  <br>    *來源位址 & 目的地 Azure 資料中心必須位於相同的國家/地區* | 支援的                                                    |
| 轉移物流（傳遞、傳輸、挑選）           | 完全由 Microsoft 管理                                     | 客戶管理                                            |
| 定價                                                      | [Azure 資料箱定價](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure 資料箱磁碟定價](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 匯入/匯出定價](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *如果您的國家/地區沒有 Azure 資料中心，則您必須將您的磁片寄送至另一個國家/地區的 Azure 資料中心。*

## <a name="next-steps"></a>後續步驟

* [以 Azure 資料箱為基礎的 MARS 代理程式離線備份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [適用于 MARS 代理程式的 Azure 匯入/匯出型離線備份](backup-azure-backup-import-export.md)  
* [適用于 MABS/DPM 的 Azure 匯入/匯出型離線備份-A](backup-azure-backup-server-import-export-.md)
