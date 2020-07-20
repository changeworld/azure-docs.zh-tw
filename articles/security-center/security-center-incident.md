---
title: 管理 Azure 資訊安全中心中的安全性事件 |Microsoft Docs
description: 本檔可協助您使用 Azure 資訊安全中心來管理安全性事件。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 28a6ea4ed40df909b4d74ff52703babb8e8cd949
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791723"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>管理 Azure 資訊安全中心中的安全性事件

即使是最熟練的安全性分析師，對安全性警示進行分級和調查也可能相當耗時。 對於許多人而言，很難知道要從何處著手。 

資訊安全中心使用[分析](security-center-detection-capabilities.md)來連接不同[安全性警示](security-center-managing-and-responding-alerts.md)之間的資訊。 資訊安全中心可以使用這些連線來提供攻擊活動的單一觀點和其相關警示，以協助您瞭解攻擊者的動作和受影響的資源。

本頁提供資訊安全中心中事件的總覽。

## <a name="what-is-a-security-incident"></a>什麼是安全性事件？

在資訊安全中心內，安全性事件是符合[攻擊鏈](alerts-reference.md#intentions)模式之資源的所有警示彙總。 事件會出現在 [[安全性警示](security-center-managing-and-responding-alerts.md)] 頁面中。 選取事件以查看相關的警示，並取得詳細資訊。

## <a name="managing-security-incidents"></a>管理安全性事件

1. 在 [資訊安全中心總覽] 頁面上，選取 [**安全性警示**] 圖格。 事件和警示會列出。 請注意，安全性事件與安全性警示有不同的圖示。

    ![查看安全性事件](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 若要查看詳細資料，請選取事件。 [**安全性事件**] 頁面會顯示更多詳細資料。 

    [![回應 Azure 資訊安全中心中的安全性事件](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    [安全性事件] 頁面的左窗格會顯示有關安全性事件的高階資訊： [標題]、[嚴重性]、[狀態]、[啟用時間]、[描述] 和受影響的資源。 在受影響的資源旁，您可以看到相關的 Azure 標記。 調查警示時，請使用這些標記來推斷資源的組織內容。

    右窗格包含 [**警示**] 索引標籤，其中包含與此事件相關的安全性警示。 

    >[!TIP]
    > 如需有關特定警示的詳細資訊，請選取它。 

    [![事件的 [採取動作] 索引標籤](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    若要切換至 [**採取動作**] 索引標籤，請選取右窗格底部的索引標籤或按鈕。 使用此索引標籤來採取進一步的動作，例如：
    - *緩和威脅*-提供此安全性事件的手動補救步驟
    - *預防未來的攻擊*-提供安全性建議，以協助減少受攻擊面、增加安全性狀態，並防止未來的攻擊
    - *觸發程式自動化回應*-提供選項來觸發邏輯應用程式，以回應此安全性事件
    - *隱藏類似警示*-提供選項，以在警示與您的組織無關時，抑制具有類似特性的未來警示 

   > [!NOTE]
   > 相同的警示可以做為事件的一部分存在，也能以獨立警示的形式顯示。

1. 若要補救事件中的威脅，請遵循每個警示所提供的補救步驟。


## <a name="next-steps"></a>後續步驟

本頁面說明資訊安全中心的安全性事件功能。 如需相關資訊，請參閱下列頁面：

* [資訊安全中心中的威脅防護](threat-protection.md)
* [資訊安全中心中的安全性警示](security-center-alerts-overview.md)
* [管理和回應安全性警示](security-center-managing-and-responding-alerts.md)