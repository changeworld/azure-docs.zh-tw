---
title: 快速入門-使用 Azure 原則部署及管理 NSG 流量記錄
titleSuffix: Azure Network Watcher
description: 本文說明如何使用內建原則來管理 NSG 流量記錄的部署
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: damendo
ms.openlocfilehash: 54b87da73d4427234e65e406d183525d55c6c00d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948539"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>快速入門：使用 Azure 原則部署及管理 NSG 流量記錄 

## <a name="overview"></a>概觀
Azure 原則有助於強制執行組織標準及大規模評估合規性。 Azure 原則的常見使用案例包括針對資源一致性、法規合規性、安全性、成本和管理來進行治理。 在本文中，我們將使用兩個可用於 NSG 流量記錄的內建原則來管理您的流程記錄設定。 第一個原則會將任何未啟用流量記錄的 Nsg 標示為旗標。 第二個原則會自動部署 Nsg 的流程記錄，而不會啟用流量記錄。 

如果您是第一次建立 Azure 原則，您可以閱讀： 
- [Azure 原則總覽](../governance/policy/overview.md) 
- [建立原則的教學](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)課程。


## <a name="locate-the-policies"></a>找出原則
1. 移至 Azure 入口網站– [portal.azure.com](https://portal.azure.com) 

在頂端搜尋列原則的首頁中搜尋原則，以流覽至 Azure 原則頁面 ![](./media/network-watcher-builtin-policy/1_policy-search.png)

2. 移至左窗格中的 [ **指派** ] 索引標籤

![[指派] 索引標籤](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. 按一下 [ **指派原則** ] 按鈕 

![指派原則按鈕](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. 按一下 [原則定義] 下的三個點功能表以查看可用的原則

5. 使用類型篩選準則，然後選擇 [內建]。 然後搜尋 "Flow log"

您應該會看到流量記錄原則清單的兩個內建原則 ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. 選擇您想要指派的原則

- 「*應針對每個網路安全性群組設定流量記錄*」是針對不符合規範的 nsg 旗標，並在未啟用流量記錄的情況下 nsg 的稽核原則
- 「*部署具有目標網路安全性群組的流量記錄資源*」是具有部署動作的原則，它會在沒有流量記錄的所有 nsg 上啟用流量記錄

以下是每個原則各有不同的指示。  

## <a name="audit-policy"></a>稽核原則 

### <a name="how-the-policy-works"></a>原則的運作方式

此原則會檢查 "Microsoft. Network/networkSecurityGroups" 類型的所有現有 ARM 物件，也就是查看指定範圍內的所有 Nsg，並透過 NSG 的 Flow Logs 屬性檢查連結流量記錄是否存在。 如果屬性不存在，則會將 NSG 標示為旗標。

如果您想要查看原則的完整定義，您可以流覽 [ [定義]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) 索引標籤，並搜尋「流程記錄」以找出原則

### <a name="assignment"></a>指派

1. 填寫您的原則詳細資料

- 範圍：訂用帳戶是常見的選擇，您也可以選擇與您相關的管理群組或資源群組。  
- 原則定義：應如「找出原則」一節所示選擇。
- AssignmentName：選擇描述性名稱 

2. 按一下 [審核 + 建立] 以查看您的指派

原則不需要任何參數。 當您指派稽核原則時，不需要填滿 [補救] 索引標籤中的詳細資料。  

![稽核原則審核](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>結果

若要檢查結果，請開啟 [合規性] 索引標籤，並搜尋您的指派名稱。
原則執行後，您應該會看到類似下列螢幕擷取畫面的內容。 如果您的原則尚未執行，請等候一段時間。 

![稽核原則結果](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>部署-如果不存在原則 

### <a name="policy-structure"></a>原則結構

此原則會檢查 "Microsoft. Network/networkSecurityGroups" 類型的所有現有 ARM 物件，也就是查看指定範圍內的所有 Nsg，並透過 NSG 的 Flow Logs 屬性檢查連結流量記錄是否存在。 如果該屬性不存在，原則就會部署流程記錄。 

如果您想要查看原則的完整定義，您可以流覽 [ [定義]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) 索引標籤，並搜尋「流程記錄」以尋找原則。 

### <a name="assignment"></a>指派

1. 填寫您的原則詳細資料

- 範圍：訂用帳戶是常見的選擇，您也可以選擇與您相關的管理群組或資源群組。  
- 原則定義：應如「找出原則」一節所示選擇。
- AssignmentName：選擇描述性名稱 

2. 新增原則參數 

網路監看員服務是一個區域服務。 這些參數可讓您執行部署流程記錄的原則動作。 
- NSG 區域：原則設為目標的 Azure 區域
- 儲存體識別碼：儲存體帳戶的完整資源識別碼。 注意：此儲存體帳戶應該與 NSG 位於相同的區域。 
- 網路監看員 RG：包含網路監看員資源的資源組名。 如果您未將它重新命名，您可以輸入 ' NetworkWatcherRG ' （預設值）。
- 網路監看員名稱：區域網路監看員服務的名稱。 格式： NetworkWatcher_RegionName。 範例： NetworkWatcher_centralus。 請參閱完整清單。

![DINE 原則參數](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. 新增補救詳細資料

- 如果您希望原則影響現有的資源，請核取 [建立補救工作] 標記 
- 應已核取 [建立受控識別]
- 選取與先前針對受控識別相同的位置 
- 您將需要參與者或擁有者許可權，才能使用此原則。 如果您有這些許可權，就不應該看見任何錯誤。

![DINE 原則補救](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. 按一下 [審核 + 建立] 以查看您的指派，您應該會看到類似下列螢幕擷取畫面的內容。

![DINE 原則評論](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>結果

若要檢查結果，請開啟 [合規性] 索引標籤，並搜尋您的指派名稱。
一旦您的原則，您應該會看到類似下列螢幕擷取畫面的畫面。 如果您的原則尚未執行，請等候一段時間。

![DINE 原則結果](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>後續步驟 

-   您可以使用 ARM 範本來部署流程記錄和流量分析，以深入瞭解此 [教學](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) 課程。
-   深入瞭解[網路](./index.yml)監看員