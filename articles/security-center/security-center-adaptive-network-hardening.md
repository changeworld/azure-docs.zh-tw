---
title: Azure 資訊安全中心中的彈性網路強化 |Microsoft Docs
description: 瞭解如何使用實際的流量模式來強化您的網路安全性群組（NSG）規則，並進一步改善您的安全性狀態。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80385073"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 資訊安全中心中的彈性網路強化
瞭解如何在 Azure 資訊安全中心中設定彈性網路強化功能。

## <a name="what-is-adaptive-network-hardening"></a>什麼是彈性網路強化？
套用[網路安全性群組（NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)來篩選進出資源的流量，可改善您的網路安全性狀態。 不過，在某些情況下，在 NSG 中的實際流量是所定義的 NSG 規則子集。 在這些情況下，您可以根據實際的流量模式來強化 NSG 規則，以進一步改善安全性狀態。

彈性網路強化會提供進一步強化 NSG 規則的建議。 它會使用機器學習演算法來決定實際的流量、已知的受信任設定、威脅情報和其他危害指標，然後提供建議，只允許來自特定 IP/埠元組的流量。

例如，假設現有的 NSG 規則是允許埠22上來自 140.20.30.10/24 的流量。 根據分析，彈性網路強化的建議是縮小範圍，並允許來自 140.23.30.10/29 的流量（這是較窄的 IP 範圍），並拒絕該埠的所有其他流量。

>[!TIP]
> 只有下列特定埠（UDP 和 TCP）支援彈性網路強化建議：13、17、19、22、23、53，69，81，111，119，123，135，137，138，139，161，162，389，445，512，514，593，636，873，1433，1434，1900，2049，2301，2323，2381，3268，3306，3389，4333，5353，5432，5555，5800，5900，5900，6379，7000，7001，7199，8081，8089，8545，9042，9160，9300，11211，16379，26379，27017，37215


![網路強化視圖](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>查看彈性網路強化警示和規則

1. 在資訊安全中心中，選取 [**網路** -> ] [彈性**網路強化**]。 網路 Vm 會列在三個不同的索引標籤底下：
   * **狀況不良的資源**：目前有執行彈性網路強化演算法所觸發之建議和警示的 vm。 
   * **狀況良好的資源**：不含警示和建議的 vm。
   * 未**掃描的資源**：由於下列其中一個原因，而無法執行調適型網路強化演算法的 vm：
      * **Vm 是傳統 vm**：僅支援 Azure Resource Manager 的 vm。
      * **沒有足夠的資料可供使用**：若要產生精確的流量強化建議，資訊安全中心需要至少30天的流量資料。
      * **VM 不受 ASC 標準保護**：只有設定為資訊安全中心標準定價層的 vm 才符合這項功能的資格。

     ![狀況不良的資源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 從 [**狀況不良的資源**] 索引標籤中，選取要查看其警示的 VM，以及要套用的建議強化規則。

    ![強化警示](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>審查並套用彈性網路強化建議規則

1. 在 [**狀況不良的資源**] 索引標籤中，選取 VM。 系統會列出警示和建議的強化規則。

     ![強化規則](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > [**規則**] 索引標籤會列出彈性網路強化建議您新增的規則。 [**警示**] 索引標籤會列出因為流量而產生的警示，而此資源不在建議規則所允許的 IP 範圍內。

2. 如果您想要變更規則的某些參數，您可以修改它，如[修改規則](#modify-rule)中所述。
   > [!NOTE]
   > 您也可以[刪除](#delete-rule)或[新增](#add-rule)規則。

3. 選取您想要在 NSG 上套用的規則，然後按一下 [**強制**]。

      > [!NOTE]
      > 強制執行的規則會新增至保護 VM 的 NSG。 （VM 可以受到與其 NIC 相關聯的 NSG，或 VM 所在的子網或兩者的保護）

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>修改規則<a name ="modify-rule"> </a>

您可能想要修改已建議的規則參數。 例如，您可能會想要變更建議的 IP 範圍。

修改彈性網路強化規則的一些重要指導方針：

* 您只能修改「允許」規則的參數。 
* 您無法將「允許」規則變更成「拒絕」規則。 

  > [!NOTE]
  > 建立和修改「拒絕」規則是直接在 NSG 上完成。 如需詳細資訊，請參閱[建立、變更或刪除網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* [**拒絕所有流量**] 規則是唯一會在此處列出的「拒絕」規則類型，而且無法修改。 不過，您可以刪除它（請參閱[刪除規則](#delete-rule)）。
  > [!NOTE]
  > 當執行演算法之後，建議使用「**拒絕所有流量**」規則，資訊安全中心不會根據現有的 NSG 設定來識別應該允許的流量。 因此，建議的規則是拒絕指定埠的所有流量。 此規則類型的名稱會顯示為「*系統產生*」。 強制執行此規則之後，其在 NSG 中的實際名稱會是由通訊協定、流量方向、「拒絕」和亂數字組成的字串。

*若要修改彈性網路強化規則：*

1. 若要修改規則的部分參數，請在 [**規則**] 索引標籤中，按一下規則列結尾的三個點（...），然後按一下 [**編輯**]。

   ![編輯規則](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在 [**編輯規則**] 視窗中，更新您想要變更的詳細資料，然後按一下 [**儲存**]。

   > [!NOTE]
   > 按一下 [**儲存**] 之後，您已成功變更規則。 *不過，您尚未將它套用至 NSG。* 若要套用它，您必須選取清單中的規則，然後按一下 [**強制**] （如下一個步驟所述）。

   ![編輯規則](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 若要套用更新的規則，請從清單中選取更新的規則，然後按一下 [**強制**]。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>新增規則<a name ="add-rule"> </a>

您可以新增資訊安全中心不建議的「允許」規則。

> [!NOTE]
> 這裡只可新增「允許」規則。 如果您想要新增「拒絕」規則，您可以直接在 NSG 上執行此動作。 如需詳細資訊，請參閱[建立、變更或刪除網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

*若要新增彈性網路強化規則：*

1. 按一下 [**新增規則**] （位於左上角）。

   ![新增規則](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在 [**新增規則**] 視窗中，輸入詳細資料，**然後按一下 [新增]**。

   > [!NOTE]
   > 按一下 [**新增**] 之後，您已成功新增規則，並列出其他建議的規則。 不過，您尚未在 NSG 上套用它。 若要啟用它，您必須在清單中選取規則，然後按一下 [**強制**] （如下一個步驟所述）。

3. 若要套用新的規則，請從清單中選取新的規則，然後按一下 [**強制**]。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>刪除規則<a name ="delete-rule"> </a>

必要時，您可以刪除目前會話的建議規則。 例如，您可能會判斷套用建議的規則可能會封鎖合法的流量。

*若要刪除您目前會話的彈性網路強化規則：*

1. 在 [**規則**] 索引標籤中，按一下規則列結尾的三個點（...），然後按一下 [**刪除**]。  

    ![強化規則](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)