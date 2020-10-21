---
title: 保護 Azure 資訊安全中心中的網路資源
description: 此文件說明可協助您保護 Azure 網路資源及遵守安全性原則的 Azure 資訊安全中心建議。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 28d0d7b14c18a1d98be1872b1555930b2e1650c3
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342445"
---
# <a name="protect-your-network-resources"></a>保護您的網路資源
Azure 資訊安全中心會持續分析 Azure 資源的安全性狀態，以獲得網路安全性最佳做法。 當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議，以加強並保護您的資源。

如需網路建議的完整清單，請參閱 [網路建議](recommendations-reference.md#recs-network)。

此文章從網路安全性觀點，說明適用於您 Azure 資源的建議。 網路建議中心圍繞下一代防火牆、網路安全性群組、JIT VM 存取、過度寬鬆的輸入流量規則等等。 如需網路建議和補救動作的清單，請參閱[管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

「安全性中心」的 **網路** 功能包括： 

- 網路地圖 (需要 Azure Defender) 
- 彈性[網路強化](security-center-adaptive-network-hardening.md) (需要 Azure Defender) 
- 網路安全性建議
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>查看您的網路資源與其建議

在 [ [資產清查] 頁面](asset-inventory.md)中，使用資源類型篩選器來選取您想要調查的網路資源：

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="資產清查網路資源類型" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>網路地圖

互動式網路地圖可透過圖形方式檢視安全性覆疊，進而為您提供可強化網路資源的的建議和見解。 您可以使用地圖查看 Azure 工作負載的網路拓撲、虛擬機器和子網路之間的連線，而且可以從地圖向下切入到特定的資源，並查看這些資源的建議。

若要開啟網路地圖：

1. 在 [安全性中心] 功能表中，開啟 Azure Defender 儀表板，然後選取 [ **網路對應**]。

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="資產清查網路資源類型" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. 選取 [ **圖層** ] 功能表選擇 [ **拓撲**]。
 
拓撲圖的預設檢視會顯示：

- 您在 Azure 中選取的訂用帳戶。 此地圖支援多個訂用帳戶。
- 不支援 (傳統 Azure 資源的 Resource Manager 資源類型的 Vm、子網和 Vnet) 
- 對等互連 Vnet
- 僅限包含嚴重性為高或中的[網路建議](security-center-recommendations.md)的資源  
- 網際網路面向資源
- 此地圖已針對您在 Azure 中選取的訂用帳戶最佳化。 如果您修改選取項目，將會根據新的設定重新計算地圖並重新最佳化。  

[![網路拓撲地圖](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>瞭解網路地圖

網路地圖可以在 **拓撲** 視圖和 **流量** 查看中顯示您的 Azure 資源。 

### <a name="the-topology-view"></a>拓撲檢視

在網路地圖的 [拓撲]**** 檢視中，您可以檢視下列關於網路資源的見解：

- 在內部圓形中，您可以看到所選訂用帳戶內的所有 Vnet，下一個圓形是所有子網路，外部圓形則是所有虛擬機器。
- 連接地圖中資源的線條可讓您知道哪些資源彼此相關聯，以及您 Azure 網路的結構。 
- 使用嚴重性指標快速了解哪些資源有來自資訊安全中心的開放式建議。
- 您可以按一下任何資源，向下切入至這些資源，並直接在網路地圖的內容中，檢視該資源的詳細資料及其建議。  
- 如果地圖上有太多要顯示的資源，Azure 資訊安全中心會使用其專屬的演算法，對您的資源進行智慧叢集，進而醒目提示處於最嚴重狀態，且包含最高嚴重性建議的資源。 

這是互動式的動態地圖，因此每個節點都可以點選，而且檢視可以根據篩選條件而變更：

1. 您可以使用最上方的篩選器修改您在網路地圖上看到的內容。 您可以根據下列項目來設定地圖的焦點：

   -  **安全性健康情況**：您可以根據 Azure 資源的嚴重性 (高、中、低) 篩選地圖。
   - **建議**：您可以根據資源的有效建議，選取要顯示的資源。 例如，您可以僅檢視資訊安全中心建議您啟用網路安全性群組的資源。
   - **網路區域**：根據預設，地圖僅會顯示網際網路面向資源，您也可以選取內部 VM。
 
2. 您隨時可以按一下左上角的 [重設]****，將地圖回復到其預設狀態。

若要向下切入到某個資源：

1. 當您在地圖上選取特定的資源時，右窗格會開啟，並提供您資源的一般資訊、連線的安全性解決方案 (如果有的話)，以及與資源相關的建議。 對於您選取的每種類型資源而言，這是相同類型的行為。 
2. 當您將滑鼠暫留在地圖中的某個節點時，您可以檢視該資源的一般資訊，包括訂用帳戶、資源類型和資源群組。
3. 使用連結可放大工具提示，並將地圖的焦點重新放在該特定節點上。 
4. 若要讓地圖的焦點遠離特定節點，則縮小。

### <a name="the-traffic-view"></a>流量檢視

[流量]**** 檢視會為您提供資源之間所有可能流量的地圖。 這會為您提供您所設定之所有規則的視覺導覽圖，而且這些規則會定義能夠與其進行通訊的資源。 這可讓您查看網路安全性群組的現有設定，以及快速找出您工作負載中可能有風險的設定。

### <a name="uncover-unwanted-connections"></a>發現來路不明的連線

此檢視的優點在於它能夠為您顯示這些允許的連線及所存在的弱點，如此您就可以使用這個橫剖面的資料，針對您的資源執行必要的強化。 

例如，您可能會偵測到兩部您沒有察覺到的電腦能進行通訊，讓您能更妥善地隔離工作負載和子網路。

### <a name="investigate-resources"></a>調查資源

若要向下切入到某個資源：

1. 當您在地圖上選取特定的資源時，右窗格會開啟，並提供您資源的一般資訊、連線的安全性解決方案 (如果有的話)，以及與資源相關的建議。 對於您選取的每種類型資源而言，這是相同類型的行為。 
2. 按一下 [流量]**** 可針對資源查看可能的輸出和輸入流量清單，這是誰可以與資源進行通訊、誰可以進行通訊，以及透過哪些通訊協定與連接埠的完整清單。 例如，當您選取 VM 時，會顯示其可以通訊的所有 Vm，而當您選取子網時，會顯示可與其通訊的所有子網。

**此資料是以網路安全性群組以及進階機器學習演算法為基礎，後者可以分析多個規則，以了解其交叉與互動。** 

[![網路流量地圖](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>後續步驟

若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

- [保護 Azure 資訊安全中心內的機器和應用程式](./asset-inventory.md)