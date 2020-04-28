---
title: 在 Azure VM 上對包含連結 VHD 的 VM 非預期重新開機進行疑難排解 | Microsoft Docs
description: 如何對 VM 的非預期重新開機進行疑難排解。
keywords: ssh 連線被拒, ssh 錯誤, azure ssh, ssh 連線失敗
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75358521"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>針對包含連結 VHD 的 VM 非預期重新開機進行疑難排解

當 Azure 虛擬機器 (VM) 有大量的連結 VHD 位於同一個儲存體帳戶中時，您可能會超出個別儲存體帳戶的延展性目標，導致 VM 非預期地重新開機。 檢查儲存體帳戶的分鐘度量（**TotalRequests**/**TotalIngress**/**TotalEgress**），以取得超過儲存體帳戶的擴充性目標的尖峰。 如需如何判斷儲存體帳戶是否發生節流作業的協助，請參閱[計量顯示 PercentThrottlingError 增加](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError)。

一般來說，VHD 每次來自虛擬機器的個別流量輸入或輸出，都會在底層的分頁 Blob 上轉換為 **Get Page** 或 **Put Page** 作業。 因此，您可以在環境中使用預估的 IOPS，依據應用程式的特定行為調節單一儲存體帳戶中應該具備的 VHD 數量。 Microsoft 建議單一儲存體帳戶中的磁碟少於 40 個。 如需標準儲存體帳戶之擴充性目標的詳細資訊，請參閱[標準儲存體帳戶的擴充性目標](../../storage/common/scalability-targets-standard-account.md)。 如需高階分頁 blob 儲存體帳戶之擴充性目標的詳細資訊，請參閱[premium 分頁 blob 儲存體帳戶的擴充性目標](../../storage/blobs/scalability-targets-premium-page-blobs.md)。

如果您超過儲存體帳戶的延展性目標，請將 VHD 放在多個的儲存體帳戶中，以減少每個個別帳戶中的活動。
