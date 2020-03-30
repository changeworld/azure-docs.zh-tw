---
title: 適用于 Microsoft Azure 的客戶密碼箱
description: Microsoft Azure 的客戶密碼箱的技術概述，該平臺提供 Microsoft 可能需要訪問客戶資料時對雲供應商訪問的控制。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561964"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>適用于 Microsoft Azure 的客戶密碼箱

> [!NOTE]
> 要使用此功能，您的組織必須具有具有最小級別的**開發人員**的[Azure 支援計畫](https://azure.microsoft.com/support/plans/)。

Microsoft Azure 的客戶密碼箱提供了一個介面，供客戶查看、批准或拒絕客戶資料訪問請求。 它用於 Microsoft 工程師需要在支援請求期間訪問客戶資料的情況。

本文介紹如何啟動、跟蹤和存儲客戶密碼箱請求，以便以後進行審核和審核。

客戶密碼箱現在通常可用，並且當前已啟用，以便遠端桌面訪問虛擬機器。

## <a name="workflow"></a>工作流程

以下步驟概述了客戶密碼箱請求的典型工作流。

1. 組織中的某個人在其 Azure 工作負荷方面出現問題。

2. 此人疑難排解但無法修復後，他們會從[Azure 門戶](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)打開支援票證。 票證分配給 Azure 客戶支援工程師。

3. Azure 支援工程師會檢查服務請求並確定解決問題的後續步驟。

4. 如果支援工程師無法使用標準工具和遙測來解決問題，則下一步是使用即時 （JIT） 訪問服務請求較高的權限。 此請求可以來自原始支援工程師。 或者，它可以來自不同的工程師，因為問題已上報到 Azure DevOps 團隊。

5. Azure 工程師提交訪問請求後，即時服務會評估請求，同時考慮到以下因素：
    - 資源的範圍
    - 要求者是隔離標識還是使用多重要素驗證
    - 權限等級

    根據 JIT 規則，此請求還可能包括來自內部 Microsoft 核准者的批准。 例如，審批人可能是客戶支援主管或 DevOps 經理。

6. 當請求需要直接存取客戶資料時，將啟動客戶密碼箱請求。 例如，遠端桌面訪問客戶的虛擬機器。

    請求現在處於 **"客戶通知"** 狀態，等待客戶批准，然後再授予存取權限。

7. 在客戶組織中，具有 Azure 訂閱[的擁有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)的使用者會收到來自 Microsoft 的電子郵件，以通知他們掛起的訪問請求。 對於客戶密碼箱請求，此人是指定的審批人。

    範例電子郵件：

    ![Azure 客戶密碼箱 - 電子郵件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 電子郵件通知提供指向 Azure 門戶中的 **"客戶鎖定框**"邊欄選項卡的連結。 使用此連結，指定的核准者登錄到 Azure 門戶以查看其組織為客戶鎖定框提供的任何掛起請求：

    ![Azure 客戶密碼箱 - 著陸頁](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   請求在客戶佇列中保留四天。 在此時間之後，訪問請求將自動過期，並且不會授予 Microsoft 工程師存取權限。

9. 要獲取掛起請求的詳細資訊，指定審批人可以從 **"掛起的請求**"中選擇密碼箱請求：

    ![Azure 客戶密碼箱 - 查看掛起的請求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的審批人還可以選擇**服務請求 ID**來查看由原始使用者創建的支援票證請求。 此資訊提供了 Microsoft 支援參與原因以及報告問題的歷史記錄的上下文。 例如：

    ![Azure 客戶密碼箱 - 查看支援票證請求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 審核請求後，指定審批人選擇**批准**或**拒絕**：

    ![Azure 客戶密碼箱 - 選擇"批准"或"拒絕"](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    作為選擇的結果：
    - **批准**：授予 Microsoft 工程師存取權限。 授予存取權限的預設期限為 8 小時。
    - **拒絕**： Microsoft 工程師的提升訪問請求被拒絕，並且不採取進一步操作。

出於審核目的，在此工作流中執行的操作將記錄在[客戶密碼箱請求日誌](#auditing-logs)中。

## <a name="auditing-logs"></a>稽核記錄

客戶密碼箱日誌存儲在活動日誌中。 在 Azure 門戶中，選擇**活動日誌**以查看與客戶密碼箱請求相關的審核資訊。 您可以篩選特定操作，例如：
- **拒絕密碼箱請求**
- **創建密碼箱請求**
- **批准密碼箱請求**
- **鎖盒請求過期**

例如：

![Azure 客戶密碼箱 - 活動日誌](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>支援的服務和方案在一般可用性

以下服務和方案當前處於客戶密碼箱的通用可用性。

### <a name="remote-desktop-access-to-virtual-machines"></a>對虛擬機器的遠端桌面訪問

客戶密碼箱當前已啟用，用於對虛擬機器的遠端桌面訪問請求。 支援以下工作負載：
- 平臺即服務 （PaaS） - Azure 雲服務（Web 角色和輔助角色）
- 基礎架構即服務 （IaaS） - Windows 和 Linux（僅限 Azure 資源管理器）
- 虛擬機器規模集 - Windows 和 Linux

> [!NOTE]
> 客戶密碼箱不支援 IaaS 經典實例。 如果在 IaaS 經典實例上運行工作負載，我們建議您將它們從經典版式遷移到資源管理器部署模型。 有關說明，請參閱[平臺支援的 IaaS 資源從經典遷移到 Azure 資源管理器](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

#### <a name="detailed-audit-logs"></a>詳細的稽核記錄

對於涉及遠端桌面訪問的方案，可以使用 Windows 事件日誌查看 Microsoft 工程師執行的操作。 請考慮使用 Azure 安全中心收集事件日誌並將資料複製到工作區進行分析。 有關詳細資訊，請參閱[Azure 安全中心中的資料收集](../../security-center/security-center-enable-data-collection.md)。

## <a name="supported-services-and-scenarios-in-preview"></a>預覽版中支援的服務和方案

以下服務當前處於"客戶密碼箱"的預覽版中：

- Azure 儲存體

- Azure SQL DB

- Azure 資料總管

- 虛擬機器（現在還涵蓋對記憶體傾印和託管磁片的訪問）

- Azure 訂閱傳輸

要為組織的這些預覽產品啟用客戶密碼箱，請註冊[Azure 公共預覽的客戶密碼箱](https://aka.ms/customerlockbox/insiderprogram)。


## <a name="exclusions"></a>排除

以下工程支援方案中不會觸發客戶密碼箱請求：

- Microsoft 工程師需要執行超出標準操作程式的活動。 例如，在意外或不可預測的情況下恢復或還原服務。

- Microsoft 工程師訪問 Azure 平臺作為故障排除的一部分，無意中可以訪問客戶資料。 例如，Azure 網路團隊執行故障排除，導致網路設備上的資料包捕獲。 但是，如果客戶在傳輸過程中對資料進行加密，工程師將無法讀取資料。

## <a name="next-steps"></a>後續步驟

客戶密碼箱自動適用于所有具有[Azure 支援計畫](https://azure.microsoft.com/support/plans/)且具有最小**開發人員級別的客戶**。

當您有符合條件的支援計畫時，您無需執行任何操作即可啟用客戶密碼箱。 如果需要此操作來進度從組織中的某個人提交的支援票證，則客戶鎖定框請求由 Microsoft 工程師啟動。
