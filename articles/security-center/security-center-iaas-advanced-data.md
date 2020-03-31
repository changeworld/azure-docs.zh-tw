---
title: Azure 安全中心中 IaaS 的高級資料安全性 |微軟文檔
description: " 瞭解如何在 Azure 安全中心中為 IaaS 啟用高級資料安全性。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282727"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虛擬機器上的 SQL 伺服器的高級資料安全性（預覽）
Azure 虛擬機器上的 SQL 伺服器的高級資料安全性是高級 SQL 安全功能的統一包。 此預覽功能包括用於識別和緩解潛在資料庫漏洞以及檢測可能指示資料庫受到威脅的異常活動的功能。 

Azure VM SQL 伺服器的此安全產品基於[Azure SQL 資料庫高級資料安全包](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)中使用的相同基本技術。


## <a name="overview"></a>總覽

高級資料安全提供了一組高級 SQL 安全功能，包括漏洞評估和高級威脅保護。

* [弱點評估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它提供了安全狀態的可見度，並包括解決安全問題和增強資料庫防禦工事的步驟。
* [高級威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)可檢測異常活動，指示訪問或利用 SQL 伺服器的異常和潛在有害嘗試。 它持續監視資料庫的可疑活動，並在異常資料庫訪問模式上提供面向操作的安全警報。 這些警報提供可疑活動詳細資訊，並建議採取行動調查和減輕威脅。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>開始在 Azure VM 上使用 SQL 的高級資料安全性

以下步驟可開始在 Azure VM 公共預覽版上使用 SQL 的高級資料安全性。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>為 Azure VM 上的 SQL 設置高級資料安全性

在訂閱/工作區級別為虛擬機器上的 SQL 伺服器啟用高級資料安全性：
 
1. 從安全中心的側邊欄，打開**定價&設置**頁面。

1. 選擇要為其啟用 Azure VM 上的 SQL 高級資料安全性的訂閱或工作區。

1. 將**VM 上 SQL 伺服器（預覽版）** 的選項切換為已啟用。 

    （按一下螢幕截圖展開）

    [![安全中心建議和警報，如 Windows 管理中心所示](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    SQL 伺服器的高級資料安全性將在連接到所選工作區或所選訂閱的預設工作區的所有 SQL 伺服器上啟用。

    >[!NOTE]
    > 首次重新開機 SQL 伺服器後，解決方案將完全處於活動狀態。 

要創建新工作區，請按照[創建日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的說明操作。

要將 SQL Server 的主機連接到工作區，請按照[將 Windows 電腦連接到 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的說明進行操作。


## <a name="set-up-email-notification-for-security-alerts"></a>為安全警報設置電子郵件通知 

您可以將收件者清單設置為在生成安全中心警報時接收電子郵件通知。 電子郵件包含指向 Azure 安全中心警報的直接連結，其中包含所有相關詳細資訊。 

1. 轉到**安全中心** > **定價&設置**，然後按一下相關訂閱

    ![訂用帳戶設定](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 在"設置"功能表中，按一下 **"電子郵件通知**"。 
1. 在 **"電子郵件地址**文本"框中，輸入電子郵件地址以接收通知。 您可以通過用逗號 （，） 分隔電子郵件地址來輸入多個電子郵件地址。  例如admin1@mycompany.com，admin2@mycompany.comadmin3@mycompany.com

    ![電子郵件設定](./media/security-center-advanced-iaas-data/email-settings.png)

1. 在**電子郵件通知**設置中，設置以下選項：
  
    * **發送高嚴重性警報的電子郵件通知**：不要為所有警報發送電子郵件，而是僅發送高嚴重性警報。
    * **還會向訂閱擁有者發送電子郵件通知**：也向訂閱擁有者發送通知。

1. 在**電子郵件通知**螢幕的頂部，按一下"**保存**"。

  > [!NOTE]
  > 請務必在關閉視窗之前按一下 **"保存**"，否則將不會保存新的**電子郵件通知**設置。

## <a name="explore-vulnerability-assessment-reports"></a>流覽漏洞評估報告

漏洞評估儀表板提供所有資料庫評估結果的概覽。 您可以根據 SQL Server 版本查看資料庫的分佈，以及失敗資料庫與傳遞資料庫的摘要以及根據風險分佈進行失敗檢查的總體摘要。

您可以直接從安全中心查看漏洞評估結果。

1. 從安全中心的側邊欄中，在"資源安全"下，選擇 **"資料存儲&存儲**"。

1. 選擇建議在**VM 中的 SQL 資料庫中的漏洞應修復（預覽）。** 有關詳細資訊，請參閱[安全中心建議](security-center-recommendations.md)。 

    [![[VM 中 SQL 資料庫中的漏洞應修復（預覽）] 建議](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    將顯示此建議的詳細視圖。

    [![應修復 VM 中 SQL 資料庫上的 [漏洞]的詳細視圖（預覽）* 建議](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 要向下切入以瞭解更多詳細資訊，

    * 有關掃描的資源（資料庫）和已測試的安全檢查清單的概述，請按一下感興趣的伺服器。
    [![由 SQL 伺服器分組的漏洞](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * 有關由特定 SQL 資料庫分組的漏洞的概述，請按一下感興趣的資料庫。
    [![由 SQL 伺服器分組的漏洞](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    在每個視圖中，安全檢查按**嚴重性**排序。 按一下特定的安全檢查以查看包含 **"說明**"、"如何**修復**它"的詳細資訊窗格以及其他相關資訊，如**影響**或**基準。**

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure VM 警報上的 SQL 伺服器的高級威脅保護
警報是由訪問或利用 SQL 伺服器的異常且可能有害的嘗試生成的。 這些事件會觸發下列警示：

### <a name="anomalous-access-pattern-alerts-preview"></a>異常訪問模式警報（預覽）

* **從異常位置訪問：** 當 SQL 伺服器的訪問模式發生更改時，將觸發此警報，此時有人從異常地理位置登錄到 SQL 伺服器。 可能的原因：
    * 攻擊者或以前的惡意雇傭人員已訪問您的 SQL Server。
    * 合法使用者已從新位置訪問 SQL Server。
* **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 可能的原因：
    * 攻擊者試圖使用常用攻擊工具破壞您的 SQL。
    * 操作中的合法滲透測試。
* **從不熟悉的主體存取**：有人使用不尋常的主體 (SQL 使用者) 登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 可能的原因：
    * 攻擊者或以前的惡意雇傭人員已訪問您的 SQL Server。 
    * 合法使用者已使用新主體訪問 SQL Server。
* **暴力 SQL 認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 可能的原因：
    * 攻擊者試圖使用暴力破壞您的 SQL。
    * 操作中的合法滲透測試。

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>潛在的 SQL 注入攻擊（在 SQL Server 2019 中支援）

* **SQL 注入的漏洞**：當應用程式在資料庫中建置錯誤的 SQL 語句時觸發此警報。 此警示表示 SQL 插入式攻擊的可能弱點。 可能的原因：
    * 應用程式的程式碼中有缺失，而建構了錯誤的 SQL 陳述式
    * 應用程式的程式碼或預存程序在建構錯誤的 SQL 陳述式時未處理使用者輸入，這可能會遭到 SQL 插入式攻擊的侵害
* **可能的 SQL 插入**：在 SQL 插入的已知應用程式弱點遭到作用中的攻擊時，會觸發此警示。 這表示有攻擊者嘗試使用有弱點的應用程式程式碼或預存程序插入惡意 SQL 陳述式。


### <a name="unsafe-command-supported-in-sql-server-2019"></a>不安全命令（在 SQL Server 2019 中支援）

* **潛在不安全操作**：在執行高度特權且可能不安全的命令時觸發此警報。 可能的原因：
    * 啟用建議禁用用於更好安全狀態的命令。
    * 攻擊者試圖利用 SQL 訪問或升級許可權。   


## <a name="explore-and-investigate-security-alerts"></a>探索和調查安全警報

您的資料安全警報可在安全中心的警報頁面、資源的安全選項卡中或通過警報電子郵件中的直接連結提供。

1. 檢視警示：

    * 在安全中心 - 按一下側邊欄**的安全警報**並選擇警報。
    * 在資源範圍內 - 打開相關資源頁，並從邊欄按一下 **"安全**"。 

1. 警報設計為自包含，每個警報都有詳細的補救步驟和調查資訊。 您可以使用其他 Azure 安全中心和 Azure 哨兵功能進行更廣泛的視圖進一步調查：

    * 啟用 SQL Server 的審核功能以進行進一步調查。 如果您是 Azure Sentinel 使用者，則可以將 SQL 稽核記錄從 Windows 安全性記錄檔事件上載到 Sentinel 並享受豐富的調查體驗。
    * 要改進安全狀態，請使用安全中心對每個警報中指示的主機的建議。 這將降低未來攻擊的風險。 


## <a name="next-steps"></a>後續步驟

有關相關材料，請參閱以下文章：

- [如何修正建議](security-center-remediate-recommendations.md)