---
title: 適用于儲存體的 Azure Defender-優點和功能
description: 瞭解適用于儲存體的 Azure Defender 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1c47c1cd464c9dc0ac8dc78f5d6f91f33481922a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307512"
---
# <a name="introduction-to-azure-defender-for-storage"></a>適用于儲存體的 Azure Defender 簡介

**適用于儲存體的 Azure Defender** 會偵測您的 Azure 儲存體帳戶可能有害的活動。 無論您的資料是儲存為 blob 容器、檔案共用或資料 lake，都可以受到保護。

這一層保護可讓您在不需要成為安全性專家的 *情況* 下解決威脅，並協助您管理安全性監視系統。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式上市 (GA)|
|定價：|**適用于儲存體的 Azure Defender**會依[定價頁面上的](security-center-pricing.md)顯示方式計費|
|受保護的儲存體類型|[Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 檔案](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) \(部分機器翻譯\)|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov，其他 Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>適用于儲存體的 Azure Defender 提供何種警示？

當發生下列情況時，就會觸發安全性警示：

- **可疑的活動** -例如，已成功存取儲存體帳戶的 IP 位址，該 IP 位址稱為 Tor 的 active exit 節點
- **異常行為** -例如，對儲存體帳戶的存取模式變更
- **可能上傳的惡意** 代碼-雜湊信譽分析指出上傳的檔案包含惡意程式碼

警示包含觸發警示的事件詳細資料，以及如何調查和修復威脅的建議。

> [!TIP]
> 您可以遵循 [此 blog 文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的指示來模擬儲存體警示。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>惡意程式碼的雜湊信譽分析是什麼？

若要判斷上傳的檔案是否可疑，Azure Defender for Storage 會使用 [Microsoft 威脅情報](https://go.microsoft.com/fwlink/?linkid=2128684)所支援的「雜湊信譽分析」。 威脅防護工具不會掃描上傳的檔案，而是會檢查儲存體記錄，並將新上傳檔案的雜湊與已知病毒、特洛伊木馬程式、間諜軟體及勒索軟體的雜湊進行比較。 

當檔案被懷疑包含惡意程式碼時，[安全性中心] 會顯示警示，並可選擇性地以電子郵件傳送儲存體擁有者，以核准刪除可疑的檔案。 若要設定自動移除雜湊信譽分析指出包含惡意程式碼的檔案，請部署 [工作流程自動化，以在包含「可能已上傳至儲存體帳戶的惡意程式碼」的警示上觸發](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。

> [!NOTE]
> 若要啟用資訊安全中心的威脅防護功能，您必須在包含適用工作負載的訂用帳戶上啟用 Azure Defender。
>
> 您可以在訂用帳戶層級或資源層級啟用 **Azure Defender 的儲存體** 。



## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Azure Defender 的儲存體。

如需相關內容，請參閱下列文章： 

- 無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。
- [如何啟用適用于儲存體的 Advanced Defender](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [適用于儲存體的 Azure Defender 警示清單](alerts-reference.md#alerts-azurestorage)
- [Microsoft 的威脅情報功能](https://go.microsoft.com/fwlink/?linkid=2128684)