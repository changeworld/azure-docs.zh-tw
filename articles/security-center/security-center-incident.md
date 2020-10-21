---
title: 管理 Azure 資訊安全中心中的安全性事件 |Microsoft Docs
description: 本檔可協助您使用 Azure 資訊安全中心管理安全性事件。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 2b5dc30de19704b5e8950515cfa6224b4bbdbaf0
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341341"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>在 Azure 資訊安全中心中管理安全性事件

分類和調查安全性警示可能相當耗時，甚至是最具技能的安全性分析師。 對許多人而言，很難知道要從哪裡著手。 

資訊安全中心會使用 [分析](./security-center-alerts-overview.md) 來連接不同 [安全性警示](security-center-managing-and-responding-alerts.md)之間的資訊。 使用這些連線，資訊安全中心可以提供攻擊活動的單一觀點，以及其相關警示，協助您瞭解攻擊者的動作和受影響的資源。

本頁提供安全性中心內事件的總覽。

## <a name="what-is-a-security-incident"></a>什麼是安全性事件？

在資訊安全中心內，安全性事件是符合[攻擊鏈](alerts-reference.md#intentions)模式之資源的所有警示彙總。 事件會出現在 [ [安全性警示](security-center-managing-and-responding-alerts.md) ] 頁面中。 選取事件以查看相關警示，並取得詳細資訊。

## <a name="managing-security-incidents"></a>管理安全性事件

1. 在 [資訊安全中心] 總覽頁面上，選取 [ **安全性警示** ] 磚。 系統會列出事件和警示。 請注意，安全性事件的安全性警示有不同的圖示。

    ![查看安全性事件](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 若要查看詳細資料，請選取事件。 [ **安全性事件** ] 頁面會顯示更多詳細資料。 

    [![回應 Azure 資訊安全中心中的安全性事件](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    [安全性事件] 頁面的左窗格會顯示有關安全性事件的高階資訊： [標題]、[嚴重性]、[狀態]、[啟用時間]、[描述] 和 [受影響的資源]。 在受影響的資源旁，您可以看到相關的 Azure 標記。 調查警示時，請使用這些標記來推斷資源的組織內容。

    右窗格包含 [ **警示** ] 索引標籤，其中包含在此事件中相互關聯的安全性警示。 

    >[!TIP]
    > 如需特定警示的詳細資訊，請加以選取。 

    [![事件的 [採取動作] 索引標籤](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    若要切換到 [ **執行動作** ] 索引標籤，請選取右窗格底部的索引標籤或按鈕。 使用此索引標籤可採取進一步的動作，例如：
    - *減輕威脅* -為此安全性事件提供手動補救步驟
    - *預防未來的攻擊* -提供安全性建議，以協助降低攻擊面、提高安全性狀態，以及防止未來的攻擊
    - *觸發程式自動回應* -提供可觸發邏輯應用程式以回應此安全性事件的選項
    - *隱藏類似警示* -提供選項，以在警示與您的組織不相關時隱藏未來具有類似特性的警示 

   > [!NOTE]
   > 相同的警示可以作為事件的一部分存在，也可以顯示為獨立警示。

1. 若要修復事件中的威脅，請遵循每個警示所提供的補救步驟。


## <a name="next-steps"></a>後續步驟

本頁面說明了安全性中心的安全性事件功能。 如需相關資訊，請參閱下列頁面：

- [安全性中心的安全性警示](security-center-alerts-overview.md)
- [管理和回應安全性警示](security-center-managing-and-responding-alerts.md)