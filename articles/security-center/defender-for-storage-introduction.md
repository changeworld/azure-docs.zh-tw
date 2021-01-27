---
title: 適用於儲存體的 Azure Defender - 優點和功能
description: 了解適用於儲存體的 Azure Defender 有何優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 675751433657a7d630d0c42482716702f520ff82
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881379"
---
# <a name="introduction-to-azure-defender-for-storage"></a>適用於儲存體的 Azure Defender 簡介


**適用於儲存體的 Azure Defender** 是 Azure 原生安全性智慧層級，用於偵測儲存體帳戶中異常且可能有害的存取意圖或攻擊。 其會利用安全性 AI 的進階功能和 [Microsoft 威脅情報](https://go.microsoft.com/fwlink/?linkid=2128684)，來提供內容安全性警示和建議。

出現異常活動時，就會觸發安全性警示。 這些警示會與 Azure 資訊安全中心整合，也會透過電子郵件將可疑活動的詳細資料，以及如何調查和修復威脅的建議傳送給訂閱系統管理員。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|**適用於儲存體的 Azure Defender** 的計費方式如 [定價頁面](security-center-pricing.md)所示|
|受保護的儲存體類型：|[Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 檔案](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) \(部分機器翻譯\)|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov、其他 Gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>適用於儲存體的 Azure Defender 有哪些優點？

適用於儲存體的 Azure Defender 可提供：

- **Azure 原生安全性** - 適用於儲存體的 Defender 可透過一鍵啟用功能，保護儲存在 Azure Blob、Azure 檔案儲存體和 Data Lake 中的資料。 作為 Azure 原生服務，適用於儲存體的 Defender 可在 Azure 管理的所有資料資產上提供集中式的資訊安全功能，並與其他 Azure 資訊安全服務 (例如 Azure Sentinel) 整合。
- **豐富的偵測套件** - 由 Microsoft 威脅情報提供技術支援，適用於儲存體的 Defender 中的偵測會涵蓋最重要的儲存體威脅，例如匿名存取、認證盜用、社交工程、權限濫用和惡意內容。
- **大規模回應** - 資訊安全中心的自動化工具可讓您更輕鬆地預防及回應已發現的威脅。 若要深入了解，請參閱[自動回應資訊安全中心觸發程序](workflow-automation.md)。

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="適用於儲存體的 Azure Defender 功能概觀":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>適用於儲存體的 Azure Defender 提供何種警示？

出現下列情況時，就會觸發安全性警示：

- **可疑的存取模式** - 例如來自 Tor 結束節點的成功存取，或來自 Microsoft 威脅情報視為可疑的 IP
- **可疑的活動** - 例如異常資料擷取或異常存取權限變更
- **上傳惡意內容** - 例如潛在惡意程式碼檔案 (根據雜湊信譽分析) 或裝載網路釣魚內容

警示中包含加以觸發之事件的詳細資料，以及如何調查和補救威脅的建議。 您可以將警示匯出至 Azure Sentinel 或其他任何第三方 SIEM 或任何其他外部工具。

> [!TIP]
> 最佳做法是在訂用帳戶層級上[設定適用於儲存體的 Azure Defender](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)，但是您也可以[在個別的儲存體帳戶加以設定](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal)。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>什麼是惡意程式碼的雜湊信譽分析？

為了判斷上傳的檔案是否可疑，適用於儲存體的 Azure Defender 會使用 [Microsoft 威脅情報](https://go.microsoft.com/fwlink/?linkid=2128684)所支援的雜湊信譽分析。 威脅防護工具不會掃描已上傳的檔案，而是會檢查儲存體記錄，並將新上傳檔案的雜湊與已知病毒、特洛伊程式、間諜軟體和勒索軟體的雜湊進行比較。 

若有檔案疑似包含惡意程式碼，資訊安全中心將會顯示警示，並可選擇性地傳送電子郵件給儲存體擁有者，供其核准刪除可疑的檔案。 若要設定對雜湊信譽分析指出包含惡意程式碼的檔案進行自動移除的這項功能，請部署[在包含「潛在惡意程式碼上傳至儲存體帳戶」的警示發生時觸發的工作流程自動化](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。

> [!NOTE]
> 若要啟用資訊安全中心的威脅防護功能，您必須在包含適用工作負載的訂用帳戶上啟用 Azure Defender。
>
> 您可以在訂用帳戶層級或資源層級啟用 **適用於儲存體的 Azure Defender**。



## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於儲存體的 Azure Defender。

如需相關內容，請參閱下列文章： 

- 無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。
- [如何啟用 Advanced Defender for Storage](../storage/common/azure-defender-storage-configure.md)
- [適用於儲存體的 Azure Defender 警示清單](alerts-reference.md#alerts-azurestorage)
- [Microsoft 的威脅情報功能](https://go.microsoft.com/fwlink/?linkid=2128684)