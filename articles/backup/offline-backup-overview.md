---
title: 離線備份的總覽
description: 瞭解離線備份的元件。 其中包括以 Azure 匯入/匯出服務為基礎的 Azure 資料箱和離線備份的離線備份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: a33810c633d8c7ca79c559355935d3c11853bc0a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160916"
---
# <a name="overview-of-offline-backup"></a>離線備份的總覽

本文提供離線備份的總覽。

Azure 的初始完整備份通常會在線上傳輸大量資料，而相較于後續只傳送增量變更的備份，則需要更多的網路頻寬。 某些地理位置中的遠端辦公室或資料中心不一定有足夠的網路頻寬。 基於這個理由，這些初始備份需要幾天的時間。 在這段期間，備份會持續使用針對內部部署資料中心內執行之應用程式所布建的相同網路。

Azure 備份支援離線備份，這會離線傳輸初始備份資料，而不會使用網路頻寬。 它提供將備份資料複製到實體存放裝置的機制。 然後，裝置會寄送至附近的 Azure 資料中心，並上傳至復原服務保存庫。 此程式可確保備份資料的健全轉移，而不需要使用任何網路頻寬。

## <a name="offline-backup-options"></a>離線備份選項

離線備份會根據存放裝置的擁有權，以兩種模式提供：

- 以 Azure 資料箱為基礎的離線備份（預覽）
- 以 Azure 匯入/匯出服務為基礎的離線備份

## <a name="offline-backup-based-on-azure-data-box-preview"></a>以 Azure 資料箱為基礎的離線備份（預覽）

Microsoft Azure 復原服務（MARS）代理程式目前支援此模式（預覽階段）。 此選項會利用[Azure 資料箱](https://azure.microsoft.com/services/databox/)，將具有 USB 連接器的 Microsoft 專屬、安全且可篡改的傳輸設備傳送至您的資料中心或遠端辦公室。 備份資料會直接寫入這些裝置上。 此選項可節省購買您自己的 Azure 相容磁片和連接器，或將暫存儲存體布建為預備位置所需的工作。 Microsoft 也會處理端對端轉移物流，您可以透過 Azure 入口網站進行追蹤。

這裡會說明使用此選項來移動備份資料的架構。

![Azure 備份資料箱架構](./media/offline-backup-overview/azure-backup-databox-architecture.png)

以下是此架構的摘要：

1. Azure 備份直接將備份資料複製到這些預先設定的裝置。
2. 接著，您可以將這些裝置寄回 Azure 資料中心。
3. Azure 資料箱會將資料複製到客戶擁有的儲存體帳戶。
4. Azure 備份會自動將備份資料從儲存體帳戶複製到指定的復原服務保存庫。 已排程增量線上備份。

若要使用以 Azure 資料箱為基礎的離線備份，請參閱[使用 Azure 資料箱的離線備份](offline-backup-azure-data-box.md)。

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>以 Azure 匯入/匯出服務為基礎的離線備份

Microsoft Azure 備份 Server （MABS）、System Center Data Protection Manager （DPM） DPM-A 和 MARS 代理程式都支援此選項。 它會使用[Azure 匯入/匯出服務](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。 您可以使用自己的 Azure 相容磁片和連接器，將初始備份資料傳輸到 Azure。 這種方法需要您布建臨時存儲區，稱為「預備位置」，並使用預先建立的公用程式來格式化備份資料，並將其複製到客戶擁有的磁片上。

這裡會說明使用此選項來移動備份資料的架構。

![Azure 備份匯入/匯出服務架構](./media/offline-backup-overview/azure-backup-import-export.png)

以下是此架構的摘要：

1. Azure 備份將備份資料寫入至預備位置，而不是透過網路傳送備份資料。
2. 預備位置中的資料會使用自訂公用程式寫入一或多個 SATA 磁片。
3. 作為準備工作的一部分，公用程式會建立 Azure 匯入作業。 SATA 磁片磁碟機會運送到最接近的 Azure 資料中心，並參考匯入工作來連接活動。
4. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
5. Azure 備份會將備份資料從儲存體帳戶複製到復原服務保存庫。 已排程增量備份。

若要使用以 Azure 匯入/匯出服務與 MARS 代理程式為基礎的離線備份，請參閱[Azure 備份中的離線備份工作流程](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)。

若要使用相同的搭配 MABS 或 DPM-A，請參閱[DPM 和 Azure 備份伺服器的離線備份工作流程](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。

## <a name="offline-backup-support-summary"></a>離線備份支援摘要

下表會比較兩個可用的選項，讓您可以根據您的案例進行適當的選擇。

| **考量**                                            | **以 Azure 資料箱為基礎的離線備份**                     | **以 Azure 匯入/匯出服務為基礎的離線備份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 備份部署模型                              | MARS 代理程式（預覽）                                              | MARS 代理程式，MABS，DPM-A                                           |
| 每一伺服器的備份資料上限（MARS）或每個保護群組（MABS、DPM-A） | [Azure 資料箱磁片](https://docs.microsoft.com/azure/databox/data-box-disk-overview)-7.2 TB <br> [Azure 資料箱](https://docs.microsoft.com/azure/databox/data-box-overview)-80 TB       | 80 TB （每個最多10個磁片，每個為 8 TB）                          |
| 安全性（資料、裝置和服務）                           | [資料](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection)-AES 256 位加密 <br> [裝置](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection)-堅固的案例、專屬、以認證為基礎的介面，用來複製資料 <br> 由 Azure 安全性功能保護的[服務](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) | 資料-BitLocker 已加密                                 |
| 暫時預備位置布建                     | 不需要                                                | 大於或等於估計的備份資料大小        |
| 支援區域                                           | [Azure 資料箱磁片區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure 資料箱區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 匯入/匯出服務區域](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 跨國家/地區運送                                     | 不受支援  <br>    來源位址與目的地 Azure 資料中心必須位於相同的國家/地區 * | 支援                                                    |
| 轉移物流（傳遞、傳輸、取貨）           | 完全由 Microsoft 管理                                     | 由客戶管理                                            |
| 定價                                                      | [Azure 資料箱定價](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure 資料箱磁片定價](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 匯入/匯出服務定價](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* 如果您的國家/地區沒有 Azure 資料中心，您必須將您的磁片寄送至另一個國家/地區的 Azure 資料中心。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 資料箱 Azure 備份離線備份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure 備份中的離線備份工作流程](backup-azure-backup-import-export.md)
- [DPM 和 Azure 備份伺服器的離線備份工作流程](backup-azure-backup-server-import-export-.md)
