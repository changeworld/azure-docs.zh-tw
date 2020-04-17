---
title: Azure Sentinel 中設定客戶管理的金鑰*微軟文件
description: 瞭解如何在 Azure Sentinel 中設定客戶管理密鑰 (CMK)。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461628"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>設定 Azure 哨兵客戶管理金鑰


本文提供後台資訊和步驟,用於為 Azure Sentinel 配置客戶管理的密鑰 (CMK)。 CMK 使儲存或發送到 Azure Sentinel 的所有資料都可以使用您創建或擁有的 Azure 密鑰保管庫金鑰在所有相關儲存資源中加密。

> [!NOTE]
> -   Azure Sentinel CMK 功能僅提供給**新**客戶,並且對此功能的訪問由 Azure 功能註冊控制。您可以通過azuresentinelCMK@microsoft.com聯繫 請求訪問,並且當容量可用時,掛起的請求將被批准。
> -   Azure 哨兵 CMK 功能僅在美國東部、美國西部 2 和中南部提供。
> -   CMK 功能僅適用於每天發送 1TB 或更多的客戶。 當您向 Microsoft 申請在 Azure 訂閱上預配 CMK 時,您將收到有關其他定價的資訊。 瞭解有關[日誌分析](../azure-monitor/platform/customer-managed-keys.md#disclaimers)充電的更多資訊。

## <a name="how-cmk-works"></a>CMK 的工作原理 

Azure Sentinel 解決方案使用多個儲存資源進行日誌收集和功能,其中包括日誌分析和其他儲存資源。 作為 Azure Sentinel CMK 設定的一部分,您也將不得不在相關儲存資源上配置 CMK 設定。 保存在日誌分析以外的存儲資源中的數據也將加密。

> [!NOTE]
> 如果在 Azure Sentinel 上啟用 CMK,則不會啟用不支援 CMK 的任何公共預覽功能。

## <a name="enable-cmk"></a>開啟 CMK 

要預配 CMK,請按照以下步驟操作: 

1.  創建 Azure 金鑰保管庫並儲存金鑰。

2.  在日誌分析工作區上啟用 CMK。

3.  註冊宇宙資料庫。

4.  向 Azure 金鑰保管庫實例添加訪問策略。

5.  在 Azure 哨兵中啟用 CMK。

6.  啟用 Azure 哨兵。

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>第一步:建立 Azure 金鑰保管庫並儲存金鑰

1.  [創建 Azure 金鑰保管庫資源](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910),然後生成或導入用於數據加密的密鑰。
    > [!NOTE]
    >  Azure 密鑰保管庫必須配置為可恢復,以保護密鑰和訪問許可權。

1.  [開啟復原選項:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   確保[軟刪除](../key-vault/general/overview-soft-delete.md)已打開。

    -   開啟[清除保護](../key-vault/general/overview-soft-delete.md#purge-protection),以防止強制刪除機密/保管庫,即使在軟刪除後也是如此。

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>第二步:在紀錄分析工作區上啟用 CMK

按照[Azure 監視器客戶管理的關鍵配置](../azure-monitor/platform/customer-managed-keys.md)中的說明操作,以創建 CMK 工作區,在以下步驟中用作 Azure Sentinel 工作區。

### <a name="step-3-register-for-cosmos-db"></a>第 3 步:註冊宇宙 DB

Azure Sentinel 與 Cosmos DB 一起用作其他儲存資源。 請確保註冊到宇宙資料庫。

按照Cosmos DB指令註冊Azure訂閱[的Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)資源提供程式。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>步驟 4:向 Azure 金鑰保管庫實例添加存取策略

請確保將從Cosmos DB到Azure密鑰保管庫實例的訪問。 按照Cosmos DB說明向Azure[密鑰保管庫實例添加具有](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)Azure宇宙資料庫主體的存取策略。

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>第 5 步:在 Azure 哨兵中啟用 CMK

Azure Sentinel CMK 功能僅在直接從 Azure 產品組接收訪問許可權後提供給新客戶。 使用 Microsoft 中的聯絡人獲得 Azure Sentinel 團隊的批准,以便在解決方案中啟用 CMK。

獲得批准後,系統將要求您提供以下資訊以啟用 CMK 功能。

-  要開啟 CMK 的工作區 ID

-  金鑰保管庫網址:將金鑰的「金鑰識別符」複製到最後一個前進斜杠:  
    

    ![金鑰識別碼](./media/customer-managed-keys/key-identifier.png)

    Azure 哨兵團隊將為提供的工作區啟用 Azure Sentinel CMK 功能。

-  Azure Sentinel 產品團隊驗證您已獲准使用此功能。 在繼續之前,您必須擁有此。

### <a name="step-6-enable-azure-sentinel"></a>第六步:啟用 Azure 哨兵


到 Azure 門戶,並在設定 CMK 的工作區上啟用 Azure Sentinel。 有關詳細資訊,請參閱[Azure 哨兵載入](quickstart-onboard.md)。

## <a name="key-encryption-key-revocation-or-deletion"></a>金鑰加密金鑰撤銷或移除


如果用戶通過刪除密鑰或刪除 Azure Sentinel 的訪問許可權來撤銷金鑰加密金鑰,Azure Sentinel 將在一小時內遵守更改,並像數據不再可用一樣運行。 此時,將阻止使用持久存儲資源(如數據引入、持久配置更改和事件創建)執行的任何操作。 以前存儲的數據不會被刪除,但仍無法訪問。 無法訪問的數據受數據保留策略的約束,並將根據該策略進行清除。

在吊銷或刪除加密密鑰後,唯一可能的操作是帳戶刪除。

如果在吊銷後恢復訪問,Azure Sentinel 將在一小時內恢復對數據的訪問。

要瞭解有關 Azure 監視器中工作原理的更多內容,請參閱[Azure 監視器 CMK 吊銷](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)。

## <a name="key-encryption-key-rotation"></a>金鑰加密金鑰


Azure 哨兵和日誌分析支援金鑰輪換。 當使用者在密鑰保管庫中執行密鑰輪換時,Azure Sentinel 會在一小時內支援新密鑰。

在金鑰保管庫中,您可以通過建立金鑰的新版本來執行金鑰輪換:

![鍵旋轉](./media/customer-managed-keys/key-rotation.png)

您可以在 24 小時後或 Azure 密鑰保管庫審核日誌不再顯示使用早期版本的任何活動後禁用金鑰的早期版本。

如果在 Azure Sentinel 和日誌分析中使用同一密鑰,則必須執行金鑰輪換,則必須使用新的 Azure 密鑰保管庫密鑰版本顯式更新日誌分析中的群集資源。 有關詳細資訊,請參閱[Azure 監視器 CMK 旋轉](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation)。

## <a name="next-steps"></a>後續步驟
在本文件中,您學習了如何在 Azure Sentinel 中設置客戶管理的密鑰。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)監視數據。

