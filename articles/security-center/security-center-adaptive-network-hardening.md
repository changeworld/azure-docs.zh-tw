---
title: Azure 資訊安全中心中的適應性網路強化 |Microsoft Docs
description: 瞭解如何使用實際的流量模式強化您的網路安全性群組 (NSG) 規則，並進一步改善安全性狀態。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: ca60d5afa38a560492c8574aadd43d6170eca253
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916179"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 資訊安全中心中的適應性網路強化
瞭解如何在安全中心設定自動調整網路強化功能。

## <a name="availability"></a>可用性
|層面|詳細資料|
|----|:----|
|版本狀態：|公開上市 (GA) |
|定價：|需要[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md)|
|必要的角色和權限：|機器 NSG 的寫入權限|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>什麼是適應性網路強化？
將 [網路安全性群組套用 (NSG) ](../virtual-network/network-security-groups-overview.md) ，以篩選進出資源的流量，進而改善您的網路安全性狀況。 但在某些情況下，透過 NSG 傳輸的實際流量仍有可能包含在已定義的 NSG 規則中。 在這些情況下，您可以根據實際的流量模式強化 NSG 規則，以進一步改善安全性態勢。

彈性網路強化可提供建議，以進一步強化 NSG 規則。 此功能會使用將實際流量、已知的信任設定、威脅情報和其他危害指標等因素納入考量的機器學習演算法，然後提供建議，而僅允許來自特定 IP/連接埠元組的流量。

例如，假設現有的 NSG 規則是允許埠22上的 140.20.30.10/24 流量。 根據流量分析，彈性網路強化可能會建議將範圍縮小以允許來自 140.23.30.10/29 的流量，並拒絕該埠的所有其他流量。

>[!Note]
> 只有下列 (適用于 UDP 和 TCP) 的特定埠支援彈性網路強化建議：13、17、19、22、23、53、69、81、111、119、123、135、137、138、139、161、162、389、445、514、593、636、873、1433、1434、1900、2049、2301、2323、2381、3268、3306、3389、4333、5353、5432、5555、5800、5900、5900、5985、5986、6379、6379、7000、7001、7199、8081、8089、8545、9042、9160、9300、11211、16379、26379、27017、37215、


## <a name="view-and-manage-hardening-alerts-and-rules"></a>查看及管理強化警示和規則

1. 從資訊安全中心的功能表，開啟 **Azure Defender** 儀表板，然後選取 [彈性網路強化] 磚 (1) ，或是與 [彈性網路強化] (2) 相關的 [見解] 面板專案。 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="存取適應性網路強化工具" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > [深入解析] 面板會顯示您的 Vm 目前使用彈性網路強化所防禦的百分比。 

1. 彈性網路強化建議的詳細資料頁面 **應該套用在網際網路對應的虛擬機器建議上** ，並將您的網路 vm 分組為三個索引標籤：
   * **狀況不良的資源**：目前有執行彈性網路強化演算法所觸發的建議和警示的 vm。 
   * **狀況良好的資源**：沒有警示和建議的 vm。
   * 未 **掃描的資源**：因為下列其中一個原因，所以無法執行調適型網路強化演算法的 vm：
      * **Vm 是傳統 vm**：僅支援 Azure Resource Manager vm。
      * **沒有足夠的資料可供使用**：為了產生精確的流量強化建議，「安全性中心」至少需要30天的流量資料。
      * **VM 未受 Azure defender 保護**：只有使用 [適用于伺服器的 Azure Defender](defender-for-servers-introduction.md) 保護的 vm 符合這項功能的資格。

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="建議彈性網路強化建議的詳細資料頁面應該套用在網際網路對應的虛擬機器上":::

1. 從 [ **狀況不良的資源** ] 索引標籤中，選取要查看其警示的 VM，以及適用的建議強化規則。

    - [ **規則** ] 索引標籤會列出自我調整網路強化建議您新增的規則
    - [ **警示** ] 索引標籤會列出因為流量而產生的警示，而這些警示不在建議規則中允許的 IP 範圍內。

1. （選擇性）編輯規則：

    - [修改規則](#modify-rule)
    - [刪除規則](#delete-rule) 
    - [新增規則](#add-rule)

3. 選取您要在 NSG 上套用的規則，然後按一下 [ **強制**]。

    > [!TIP]
    > 如果允許的來源 IP 範圍顯示為 [無]，表示建議的規則是 *拒絕* 規則，否則為 *允許* 規則。

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="管理適應性網路強化規則":::

      > [!NOTE]
      > 強制執行的規則會新增至 NSG (s) 保護 VM。  (VM 可以受到與其 NIC 相關聯的 NSG，或 VM 所在的子網，或兩者) 

### <a name="modify-a-rule"></a>修改規則<a name ="modify-rule"></a>

您可能會想要修改建議之規則的參數。 例如，您可能想要變更建議的 IP 範圍。

修改適應性網路強化規則的一些重要指導方針：

* 您只能修改「允許」規則的參數。 
* 您無法將「允許」規則變更為「拒絕」規則。 

  > [!NOTE]
  > 建立和修改「拒絕」規則會直接在 NSG 上完成。 如需詳細資訊，請參閱 [建立、變更或刪除網路安全性群組](../virtual-network/manage-network-security-group.md)。

* **拒絕所有流量** 規則是唯一會在此列出的「拒絕」規則類型，而且無法修改。 不過，您可以刪除它 (請參閱 [刪除規則](#delete-rule)) 。
  > [!NOTE]
  > 基於執行演算法的結果，資訊安全中心不會根據現有的 NSG 設定來識別應允許的流量，因此建議使用「 **拒絕所有流量** 」規則。 因此，建議的規則是拒絕指定埠的所有流量。 此類型規則的名稱會顯示為「*系統產生*」。 在強制執行此規則之後，在 NSG 中的實際名稱將會是由通訊協定、流量方向、「拒絕」和亂數字組成的字串。

*修改適應性網路強化規則：*

1. 若要修改規則的某些參數，請在 [ **規則** ] 索引標籤中，按一下規則資料列結尾的三個點 ( ... ) ，然後按一下 [ **編輯**]。

   ![編輯 s 規則](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在 [ **編輯規則** ] 視窗中，更新您想要變更的詳細資料，然後按一下 [ **儲存**]。

   > [!NOTE]
   > 按一下 [ **儲存**] 之後，您已成功變更規則。 *不過，您未將它套用至 NSG。* 若要套用它，您必須在清單中選取規則，然後選取 [ **強制執行** (，如下一步) 所述。

   ![選取 [儲存]](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 若要套用更新的規則，請從清單中選取更新的規則，然後按一下 [ **強制**]。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>新增規則<a name ="add-rule"></a>

您可以新增安全性中心不建議的「允許」規則。

> [!NOTE]
> 此處只可新增「允許」規則。 如果您想要新增「拒絕」規則，您可以直接在 NSG 上這麼做。 如需詳細資訊，請參閱 [建立、變更或刪除網路安全性群組](../virtual-network/manage-network-security-group.md)。

*新增調適型網路強化規則：*

1. 按一下位於左上角的 [ **新增規則** (]) 。

   ![新增規則](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在 [ **新增規則** ] 視窗中，輸入詳細資料， **然後按一下 [新增]**。

   > [!NOTE]
   > 按一下 [ **新增**] 之後，您已成功新增規則，而且會列出其他建議的規則。 不過，您尚未將它套用至 NSG。 若要啟用它，您必須在清單中選取規則，然後按一下 [ **強制執行** (，如下一步) 所述。

3. 若要套用新規則，請從清單中選取新規則，然後按一下 [ **強制**]。

    ![強制執行規則](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>刪除規則<a name ="delete-rule"></a>

如有必要，您可以刪除目前會話的建議規則。 例如，您可能會判斷套用建議的規則可能會封鎖合法的流量。

*若要刪除目前會話的彈性網路強化規則：*

1. 在 [ **規則** ] 索引標籤中，按一下規則資料列結尾的三個點 ( ... ) ，然後按一下 [ **刪除**]。  

    ![刪除規則](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)