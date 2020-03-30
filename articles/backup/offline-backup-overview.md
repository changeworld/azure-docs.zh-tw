---
title: 離線備份概述
description: 瞭解離線備份的元件。 它們包括基於 Azure 資料框的離線備份和基於 Azure 導入/匯出服務的離線備份。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196145"
---
# <a name="overview-of-offline-backup"></a>離線備份概述

本文概述了離線備份。

與僅傳輸增量更改的後續備份相比，對 Azure 的初始完整備份通常線上傳輸大量資料，並需要更多的網路頻寬。 某些地區的遠端辦公室或資料中心並不總是有足夠的網路頻寬。 因此，這些初始備份需要幾天時間。 在此期間，備份持續使用為在本地資料中心中運行的應用程式預配的相同網路。

Azure 備份支援離線備份，該備份離線傳輸初始備份資料，而無需使用網路頻寬。 它提供了將備份資料複製到物理存放裝置的機制。 然後，這些設備將發送到附近的 Azure 資料中心，並上載到恢復服務保存庫。 此過程可確保在不使用任何網路頻寬的情況下可靠傳輸備份資料。

## <a name="offline-backup-options"></a>離線備份選項

根據存放裝置的擁有權，以兩種模式提供離線備份：

- 基於 Azure 資料框的離線備份（預覽）
- 基於 Azure 導入/匯出服務的離線備份

## <a name="offline-backup-based-on-azure-data-box-preview"></a>基於 Azure 資料框的離線備份（預覽）

當前 Microsoft Azure 恢復服務 （MARS） 代理在預覽版中支援此模式。 此選項利用 Azure[資料盒](https://azure.microsoft.com/services/databox/)將 Microsoft 專有、安全和防篡改傳輸設備與 USB 連接器運送到資料中心或遠端辦公室。 備份資料直接寫入這些設備。 此選項可節省採購自己的 Azure 相容磁片和連接器或將臨時存儲預配為暫存位置所需的工作量。 Microsoft 還處理端到端傳輸物流，您可以通過 Azure 門戶跟蹤該物流。 

此處顯示了一種使用此選項描述備份資料移動的體系結構。

![Azure 備份資料框體系結構](./media/offline-backup-overview/azure-backup-databox-architecture.png)

以下是體系結構的摘要：

1. Azure 備份直接將備份資料複製到這些預配置的設備。
2. 然後，可以將這些設備運回 Azure 資料中心。
3. Azure 資料盒將資料複製到客戶擁有的存儲帳戶。
4. Azure 備份會自動將備份資料從存儲帳戶複製到指定的恢復服務保存庫。 計畫增量連線備份。

要使用基於 Azure 資料框的離線備份，請參閱[使用 Azure 資料框離線備份](offline-backup-azure-data-box.md)。

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>基於 Azure 導入/匯出服務的離線備份

Microsoft Azure 備份伺服器 （MABS）、系統中心資料保護管理器 （DPM） DPM-A 和 MARS 代理支援此選項。 它使用[Azure 導入/匯出服務](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。 可以使用自己的與 Azure 相容的磁片和連接器將初始備份資料傳輸到 Azure。 此方法要求您預配臨時存儲（稱為暫存位置）並使用預構建的實用程式格式化備份資料並將其複製到客戶擁有的磁片上。 

此處顯示了一種使用此選項描述備份資料移動的體系結構。

![Azure 備份導入/匯出服務體系結構](./media/offline-backup-overview/azure-backup-import-export.png)

以下是體系結構的摘要：

1. Azure 備份不是通過網路發送備份資料，而是將備份資料寫入暫存位置。
2. 過渡位置中的資料通過使用自訂實用程式寫入一個或多個 SATA 磁片。
3. 作為準備工作的一部分，實用程式將創建 Azure 導入作業。 SATA 磁碟機將發送到最近的 Azure 資料中心，並引用導入作業來連接活動。
4. 在 Azure 資料中心，磁碟上的資料會複製到 Azure 儲存體帳戶。
5. Azure 備份將備份資料從存儲帳戶複製到恢復服務保存庫。 計畫增量備份。

要將基於 Azure 導入/匯出服務與 MARS 代理的離線備份使用，請參閱[Azure 備份 中的離線備份工作流](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)。

要與 MABS 或 DPM-A 一起使用，請參閱[DPM 和 Azure 備份伺服器的離線備份工作流](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。

## <a name="offline-backup-support-summary"></a>離線備份支援摘要

下表比較了兩個可用選項，以便您可以根據方案做出適當的選擇。

| **考量**                                            | **基於 Azure 資料框的離線備份**                     | **基於 Azure 導入/匯出服務的離線備份**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 備份部署模型                              | MARS 代理（預覽版）                                              | MARS 代理，MABS，DPM-A                                           |
| 每個伺服器 （MARS） 或每個保護組的最大備份資料（MABS、DPM-A） | [Azure 資料盒磁片](https://docs.microsoft.com/azure/databox/data-box-disk-overview)- 7.2 TB <br> [Azure 資料框](https://docs.microsoft.com/azure/databox/data-box-overview)- 80 TB       | 80 TB（每個磁片最多 10 個，每個磁片 8 TB）                          |
| 安全性（資料、設備和服務）                           | [資料](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection)- AES 256 位加密 <br> [設備](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection)- 堅固的外殼、專有的、基於憑據的介面，用於複製資料 <br> [服務](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection)- 受 Azure 安全功能保護 | 資料 - 已加密的位鎖                                 |
| 臨時暫存位置預配                     | 不需要                                                | 大於或等於估計的備份資料大小        |
| 支援區域                                           | [Azure 資料框磁片區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure 資料框區域](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 導入/匯出服務區域](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 跨國運輸                                     | 不支援  <br>    源位址和目標 Azure 資料中心必須位於同一國家/地區* | 支援                                                    |
| 轉運物流（送貨、運輸、取件）           | 完全微軟管理                                     | 由客戶管理                                            |
| 定價                                                      | [Azure 資料框定價](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure 資料盒磁片定價](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 導入/匯出服務定價](https://azure.microsoft.com/pricing/details/storage-import-export/) |

•如果您的國家/地區沒有 Azure 資料中心，則需要將磁片運送到另一個國家/地區的 Azure 資料中心。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料框進行 Azure 備份離線備份](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure 備份中的離線備份工作流](backup-azure-backup-import-export.md) 
* [DPM 和 Azure 備份伺服器的離線備份工作流](backup-azure-backup-server-import-export-.md)
