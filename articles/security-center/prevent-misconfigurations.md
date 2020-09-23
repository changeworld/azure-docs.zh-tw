---
title: 如何避免 Azure 資訊安全中心的錯誤配置
description: 瞭解如何在 [建議詳細資料] 頁面上使用資訊安全中心的 [強制執行] 和 [拒絕] 選項
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906386"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>使用強制/拒絕建議防止錯誤配置

安全性錯誤配置是安全性事件的主要原因。 資訊安全中心現在可以協助 *防止* 新資源在特定建議中的錯誤配置。 

這項功能可協助保護您的工作負載，並穩定您的安全分數。

根據特定的建議來強制執行安全設定，會以兩種模式提供：

- 使用 Azure 原則的 **拒絕** 效果，您可以停止建立狀況不良的資源
- 使用 [ **強制** ] 選項，您可以利用 Azure 原則的 **DeployIfNotExist** 效果，並在建立時自動補救不符合規範的資源

這可以在 [資源詳細資料] 頁面頂端找到所選的安全性建議 (查看 [ [拒絕/強制執行選項](#recommendations-with-denyenforce-options) ]) 的建議。

## <a name="prevent-resource-creation"></a>防止建立資源

1. 開啟新資源必須滿足的建議，然後選取頁面頂端的 [ **拒絕** ] 按鈕。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="已反白顯示 [拒絕] 按鈕的建議頁面":::

    [設定] 窗格隨即開啟，並列出範圍選項。 

1. 選取相關的訂用帳戶或管理群組來設定領域。

    > [!TIP]
    > 您可以使用資料列結尾的三個點來變更單一訂用帳戶，或使用核取方塊來選取多個訂閱或群組，然後選取 [ **變更為拒絕**]。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="設定 Azure 原則拒絕的範圍":::


## <a name="enforce-a-secure-configuration"></a>強制執行安全設定

1. 開啟您將部署範本部署的建議，如果新資源無法滿足此部署，請選取頁面頂端的 [ **強制執行** ] 按鈕。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="反白顯示 [強制執行] 按鈕的建議頁面":::

    [設定] 窗格隨即開啟，其中包含所有原則設定選項。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="強制執行設定選項":::

1. 設定範圍、指派名稱和其他相關選項。

1. 選取 [檢閱 + 建立]。

## <a name="recommendations-with-denyenforce-options"></a>具有拒絕/強制執行選項的建議

這些建議可與 **拒絕** 選項搭配使用：

- 虛擬機器應移轉到新的 Azure Resource Manager 資源
- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源
- 除了 RootManageSharedAccessKey 外，應從事件中樞命名空間移除所有授權規則
- 除了 RootManageSharedAccessKey 外，應從服務匯流排命名空間移除所有授權規則
- 應啟用儲存體帳戶的安全傳輸
- 應該只允許對 Redis Cache 的安全連線
- 應加密自動化帳戶變數
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 稽核不受限制的儲存體帳戶網路存取


這些建議可搭配 [ **強制** ] 選項使用：

- 應在 Logic Apps 中啟用診斷記錄
- 應在 Data Lake Analytics 中啟用診斷記錄
- 應在 IoT 中樞內啟用診斷記錄
- 應啟用 Batch 帳戶中的診斷記錄
- 應啟用 Azure 串流分析中的診斷記錄
- 應在服務匯流排中啟用診斷記錄
- 應在搜尋服務中啟用診斷記錄
- 應啟用事件中樞內的診斷記錄
- 應在虛擬機器擴展集中啟用診斷記錄
- 應啟用 Key Vault 中的診斷記錄
- 應啟用 SQL 伺服器上的稽核
- 應在 SQL 伺服器上啟用進階資料安全性



