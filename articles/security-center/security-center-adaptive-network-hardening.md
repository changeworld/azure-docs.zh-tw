---
title: Azure 安全中心的自我調整網路強化 |微軟文檔
description: 瞭解如何使用實際流量模式強化網路安全性群組 （NSG） 規則並進一步改善安全狀況。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385073"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 安全中心的自我調整網路強化
瞭解如何在 Azure 安全中心配置自我調整網路強化。

## <a name="what-is-adaptive-network-hardening"></a>什麼是自我調整網路強化？
應用[網路安全性群組 （NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)來過濾資源中和來自資源的流量，從而改善網路安全狀況。 但是，在某些情況經 NSG 的實際流量仍然是定義的 NSG 規則的子集。 在這些情況下，可以通過根據實際流量模式強化 NSG 規則來進一步改善安全狀態。

自我調整網路強化提供了進一步強化 NSG 規則的建議。 它使用機器學習演算法，該演算法將實際流量、已知可信配置、威脅情報和其他危害指標的因素考慮在內，然後提供建議，僅允許來自特定 IP/埠組合的流量。

例如，假設現有的 NSG 規則允許在埠 22 上從 140.20.30.10/24 的流量。 自我調整網路強化的建議，基於分析，將縮小範圍，並允許流量從 140.23.30.10/29 - 這是一個較窄的 IP 範圍，並拒絕所有其他流量到該埠。

>[!TIP]
> 自我調整網路強化建議僅在以下特定埠（對於 UDP 和 TCP）上支援：13、 17、 19， 22， 23， 53， 69， 81， 111， 119， 123， 135， 135， 137， 138， 139， 161， 162， 389， 445， 512， 514， 593， 636， 873， 1433， 1434， 1900， 2049， 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![網路強化視圖](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>查看自我調整網路強化警報和規則

1. 在安全中心中，選擇**網路** -> **自我調整網路強化**。 網路 VM 列在三個單獨的選項卡下：
   * **不正常資源**：當前具有通過運行自我調整網路強化演算法觸發的建議和警報的 VM。 
   * **健康資源**：沒有警報和建議的 VM。
   * **未掃描資源**：自我調整網路強化演算法由於以下原因之一無法運行的 VM：
      * **VM 是經典 VM：** 僅支援 Azure 資源管理器 VM。
      * **資料不足**：為了生成準確的流量強化建議，安全中心至少需要 30 天的流量資料。
      * **VM 不受 ASC 標準的保護**：只有設置為安全中心標準定價層的 VM 才有資格享受此功能。

     ![不健康的資源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 從 **"不正常資源**"選項卡中，選擇一個 VM 以查看其警報和要應用的建議強化規則。

    ![強化警報](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>查看並應用自我調整網路強化建議規則

1. 從 **"不正常的資源**"選項卡中選擇 VM。 列出了警報和建議的強化規則。

     ![硬化規則](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > "**規則"** 選項卡列出了自我調整網路強化建議您添加的規則。 "**警報"** 選項卡列出了由於流量而生成的警報，這些警報流到資源，而資源不在建議規則中允許的 IP 範圍內。

2. 如果要更改規則的某些參數，可以對其進行修改，如[修改規則 中](#modify-rule)所述。
   > [!NOTE]
   > 您還可以[刪除](#delete-rule)或[添加](#add-rule)規則。

3. 選擇要在 NSG 上應用的規則，然後按一下 **"強制**"。

      > [!NOTE]
      > 強制規則將添加到保護 VM 的 NSG 中。 （VM 可以受與其 NIC 關聯的 NSG 或 VM 所在的子網或兩者同時受到保護）

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>修改規則<a name ="modify-rule"> </a>

您可能需要修改已建議的規則的參數。 例如，您可能希望更改建議的 IP 範圍。

修改自我調整網路強化規則的一些重要準則：

* 只能修改"允許"規則的參數。 
* 您不能將"允許"規則更改為"拒絕"規則。 

  > [!NOTE]
  > 創建和修改"拒絕"規則直接在 NSG 上完成。 有關詳細資訊，請參閱[創建、更改或刪除網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* **拒絕所有流量**規則是此處列出的"拒絕"規則的唯一類型，無法修改。 但是，您可以刪除它（請參閱[刪除規則](#delete-rule)）。
  > [!NOTE]
  > 當由於運行演算法的結果，安全中心不根據現有的 NSG 配置標識應允許的流量時，建議使用 **"拒絕所有流量**規則"。 因此，建議的規則是拒絕到指定埠的所有流量。 這種類型的規則的名稱顯示為"*系統生成*"。 執行此規則後，其在 NSG 中的實際名稱將是一個由協定、流量方向、"DENY"和亂數組成的字串。

*要修改自我調整網路強化規則，*

1. 要修改規則的某些參數，**請在"規則"** 選項卡中按一下規則行末尾的三個點 （...），然後按一下"**編輯**"。

   ![編輯規則](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在 **"編輯規則"** 視窗中，更新要更改的詳細資訊，然後按一下"**保存**"。

   > [!NOTE]
   > 按一下 **"保存"** 後，已成功更改規則。 *但是，您尚未將其應用於 NSG。* 要應用它，您必須挑選清單中的規則，然後按一下 **"強制"（** 如下一步所述）。

   ![編輯規則](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 要應用更新的規則，請從清單中選擇更新的規則，然後按一下 **"強制**"。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>添加新規則<a name ="add-rule"> </a>

您可以添加安全中心不建議的"允許"規則。

> [!NOTE]
> 此處只能添加"允許"規則。 如果要添加"拒絕"規則，可以直接在 NSG 上添加。 有關詳細資訊，請參閱[創建、更改或刪除網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

*要添加自我調整網路強化規則，*

1. 按一下 **"添加規則**"（位於左上角）。

   ![添加規則](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在 **"新建規則"** 視窗中，輸入詳細資訊，然後按一下"**添加**"。

   > [!NOTE]
   > 按一下 **"添加**"後，您已成功添加規則，並且該規則將與其他建議的規則一起列出。 但是，您尚未將其應用於 NSG。 要啟動它，您必須挑選清單中的規則，然後按一下 **"強制"（** 如下一步所述）。

3. 要應用新規則，請從清單中選擇新規則，然後按一下 **"強制**"。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>刪除規則<a name ="delete-rule"> </a>

如有必要，可以刪除當前會話的建議規則。 例如，您可以確定應用建議的規則可能會阻止合法流量。

*要刪除當前會話的自我調整網路強化規則，請進行：*

1. 在 **"規則"** 選項卡中，按一下規則行末尾的三個點 （...），然後按一下 **"刪除**"。  

    ![硬化規則](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)