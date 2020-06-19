---
title: 離線備份概觀
description: 了解離線備份的元件。 其中包括以 Azure 資料箱為基礎的離線備份，和以 Microsoft Azure 匯入/匯出服務為基礎的離線備份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: c3ef79136ca32c24ae6d16057d3195b57e8ced2d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744647"
---
# <a name="overview-of-offline-backup"></a>離線備份概觀

本文提供離線備份的概觀。

Azure 的初始完整備份通常會在線上傳輸大量資料，且需要較多網路頻寬，相較之下，後續備份只會傳輸增量變更。 某些地理位置中的遠端辦公室或資料中心不一定有足夠的網路頻寬。 基於這個理由，這些初始備份需要幾天的時間。 在這段期間，備份會持續使用相同網路，而這個網路是針對在內部部署資料中心執行的應用程式所佈建。

Azure 備份支援離線備份，這會離線傳輸初始備份資料，而不會使用網路頻寬。 提供將備份資料複製到實體儲存體裝置的機制。 然後，裝置會寄送至附近的 Azure 資料中心，並上傳至復原服務保存庫。 此程序可確保備份資料的健全傳輸，而不需要使用任何網路頻寬。

## <a name="offline-backup-options"></a>離線備份選項

離線備份會根據儲存體裝置的擁有權，以兩種模式提供：

- 以 Azure 資料箱為基礎的離線備份 (預覽)
- 以 Azure 匯入/匯出服務為基礎的離線備份

## <a name="offline-backup-based-on-azure-data-box-preview"></a>以 Azure 資料箱為基礎的離線備份 (預覽)

此模式目前支援 Microsoft Azure 復原服務 (MARS) 代理程式 (預覽)。 此選項會利用 [Azure 資料箱](https://azure.microsoft.com/services/databox/)，將具有 USB 連接器的 Microsoft 專屬、安全且可防竄改的傳輸設備運送至您的資料中心或遠端辦公室。 備份資料會直接寫入這些裝置上。 此選項可節省購買您自己的 Azure 相容磁碟和連接器，或將暫存儲存體佈建為暫存位置所需的工作。 Microsoft 也會處理端對端傳輸物流，您可以透過 Azure 入口網站進行追蹤。

這裡會說明使用此選項來移動備份資料的架構。

![Azure 備份資料箱架構](./media/offline-backup-overview/azure-backup-databox-architecture.png)

以下是架構的摘要：

1. Azure 備份會直接將備份資料複製到這些預先設定的裝置。
2. 接著，您可以將這些裝置寄回 Azure 資料中心。
3. Azure 資料箱會將資料複製到客戶擁有的儲存體帳戶。
4. Azure 備份會自動將備份資料從儲存體帳戶複製到指定的復原服務保存庫。 已排程增量線上備份。

若要使用以 Azure 資料箱為基礎的離線備份，請參閱[使用 Azure 資料箱進行離線備份](offline-backup-azure-data-box.md)。

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>以 Azure 匯入/匯出服務為基礎的離線備份

Microsoft Azure 備份伺服器 (MABS)、System Center Data Protection Manager (DPM) DPM-A 和 MARS 代理程式都支援此選項。 使用 [Azure 匯入/匯出服務](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。 您可以使用自己的 Azure 相容磁碟和連接器，將初始備份資料傳輸到 Azure。 這種方法需要您佈建暫存儲存體，稱為「暫存位置」，並使用預先建置的公用程式來格式化備份資料，並將其複製到客戶擁有的磁碟上。

這裡會說明使用此選項來移動備份資料的架構。

![Azure 備份匯入/匯出服務架構](./media/offline-backup-overview/azure-backup-import-export.png)

以下是架構的摘要：

1. Azure 備份會將備份資料寫入到暫存位置，而不是透過網路傳送備份資料。
2. 系統會使用自訂公用程式，將暫存位置中的資料寫入一或多個 SATA 磁碟。
3. 在準備工作中，公用程式會建立 Azure 匯入作業。 將 SATA 磁碟機運送至最接近的 Azure 資料中心，並參考匯入作業以連線活動。
4. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
5. Azure 備份會將儲存體帳戶中的資料複製到復原服務保存庫。 已排程增量備份。

若要透過 MARS 代理程式使用以 Azure 匯入/匯出服務為基礎的離線備份，請參閱 [Azure 備份中的離線備份工作流程](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)。

若要使用 MABS 或 DPM-A 執行相同的作業，請參閱 [DPM 和 Azure 備份伺服器的離線備份工作流程](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。

## <a name="offline-backup-support-summary"></a>離線備份支援摘要

下表會比較兩個可用的選項，讓您可以根據您的案例進行適當的選擇。

| **考量**                                            | **以 Azure 資料箱為基礎的離線備份**                     | **以 Azure 匯入/匯出服務為基礎的離線備份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 備份部署模型                              | MARS 代理程式 (預覽)                                              | MARS 代理程式、MABS、DPM-A                                           |
| 每一伺服器 (MARS) 或每個保護群組 (MABS、DPM-A) 的備份資料上限 | [Azure 資料箱磁碟](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2 TB <br> [Azure 資料箱](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (最多 10 個磁碟，每個磁碟 8 TB)                          |
| 安全性 (資料、裝置和服務)                           | [資料](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256 位元加密 <br> [裝置](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - 用來複製資料的堅固外盒、專屬、以認證為基礎的介面 <br> [服務](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - 受到 Azure 安全功能的保護 | 資料 - BitLocker 加密                                 |
| 暫時暫存位置佈建                     | 不需要                                                | 大於或等於估計的備份資料大小        |
| 支援區域                                           | [Azure 資料箱磁碟區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure 資料箱區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 匯入/匯出服務區域](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 跨國家/地區運送                                     | 不支援  <br>    來源地址和目的地 Azure 資料中心必須位於相同的國家/地區* | 支援                                                    |
| 傳輸物流 (傳遞、傳輸、取貨)           | 完整 Microsoft 受控                                     | 由客戶管理                                            |
| 定價                                                      | [Azure 資料箱定價](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure 資料箱磁碟定價](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 匯入/匯出服務定價](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*如果您的國家/地區沒有 Azure 資料中心，您必須將您的磁碟寄送至另一個國家/地區的 Azure 資料中心。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 資料箱進行 Azure 備份離線備份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure 備份中的離線備份工作流程](backup-azure-backup-import-export.md)
- [適用於 DPM 和 Azure 備份伺服器的離線備份工作流程](backup-azure-backup-server-import-export-.md)
