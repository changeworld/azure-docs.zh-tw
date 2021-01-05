---
title: 將 Cherwell 與 IT 服務管理連接器連線
description: 本文提供有關如何使用 IT 服務管理連接器 Cherwell 的資訊， (ITSMC) 在 Azure 監視器中，以集中監視及管理 ITSM 工作專案。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 73fc13cf2a49d7cacd7540d06c6d0afd9cea68e5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729636"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>將 Cherwell 與 IT 服務管理連接器連線

本文提供有關如何設定 Cherwell 實例與 IT 服務管理連接器之間的連線 (ITSMC) Log Analytics 中的連線，以集中管理您的工作專案。

> [!NOTE]
> 我們建議 Cherwell 和 Provance 客戶使用 [Webhook 動作](./action-groups.md#webhook) ，以 Cherwell 和 Provance 端點作為整合的另一種解決方案。

下列各節提供有關如何將 Cherwell 產品連線到 Azure 中的 ITSMC 之詳細資料。

## <a name="prerequisites"></a>Prerequisites

請確保已符合下列必要條件︰

- 已安裝 ITSMC。 詳細資訊：[新增 IT 服務管理連接器解決方案](./itsmc-definition.md#add-it-service-management-connector)。
- 所產生的用戶端識別碼。 詳細資訊：[產生 Cherwell 的用戶端識別碼](#generate-client-id-for-cherwell)。
- 使用者角色：系統管理員。

## <a name="connection-procedure"></a>連線程序

請使用下列程序來建立 Cherwell 連線：

1. 在 Azure 入口網站中，移至 [所有資源]，然後尋找 **ServiceDesk(YourWorkspaceName)**

2. 在 [工作區資料來源] 下方，按一下 [ITSM 連線]。
    ![新增連線](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格頂端按一下 [新增]。

4. 提供下表中所述的資訊，然後按一下 [確定] 來建立連線。

> [!NOTE]
> 這些全部都是必要參數。

| **欄位** | **說明** |
| --- | --- |
| **連接名稱**   | 輸入您想要與 ITSMC 連線之 Cherwell 執行個體的名稱。  稍後當您在這個 ITSM 中設定工作項目 / 檢視詳細的記錄分析時，會使用這個名稱。 |
| **夥伴類型**   | 選取 [Cherwell]。 |
| **使用者名稱**   | 輸入可以連線到 ITSMC 的 Cherwell 使用者名稱。 |
| **密碼**   | 將與此使用者名稱與相關聯的密碼輸入。 **注意：** 使用者名稱和密碼僅用來產生驗證權杖，並不會儲存在 ITSMC 服務內。|
| **伺服器 URL**   | 輸入您想要連線到 ITSMC 之 Cherwell 執行個體的 URL。 |
| **用戶端識別碼**   | 將您在 Cherwell 執行個體中產生的用戶端識別碼輸入以驗證此連線。   |
| **資料同步範圍**   | 選取您想要透過 ITSMC 同步的 Cherwell 工作項目。  系統會將這些工作項目匯入記錄分析。   **選項︰** 事件、變更要求。 |
| **同步資料** | 輸入您想要起算資料的過去天數。 **上限**：120 天。 |
| **在 ITSM 解決方案中建立新的設定項目** | 如果您想要在 ITSM 產品中建立設定項目，請選取此選項。 選取時，ITSMC 會在支援的 ITSM 系統中建立受影響的 CI 作為設定項目 (如果 CI 不存在)。 **預設**︰停用。 |

![Cherwell 連接](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**順利連線並同步處理時**︰

- 從這個 Cherwell 執行個體選取的工作項目會匯入到 Azure **Log Analytics**。 您可以在 [IT 服務管理連接器] 圖格上檢視這些工作項目的摘要。

- 您可以在這個 Cherwell 執行個體中建立來自 Log Analytics 警示、記錄檔記錄或 Azure 警示的事件。

深入了解：[從 Azure 警示建立 ITSM 工作項目](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

### <a name="generate-client-id-for-cherwell"></a>產生 Cherwell 的用戶端識別碼

若要產生用戶端識別碼/Cherwell 的金鑰，請使用下列程序︰

1. 以管理員身分登入您的 Cherwell 執行個體。
2. 按一下 [安全性] > [編輯 REST API 用戶端設定]。
3. 選取 [建立新的用戶端] > [用戶端祕密]。

    ![Cherwell 使用者識別碼](media/itsmc-connections/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>後續步驟

* [ITSM 連接器總覽](itsmc-overview.md)
* [建立 Azure 警示的 ITSM 工作項目](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)