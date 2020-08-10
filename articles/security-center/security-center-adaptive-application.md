---
title: Azure 資訊安全中心的自適性應用程式控制
description: 本檔可協助您使用 Azure 資訊安全中心中的調適型應用程式控制，以允許列出在 Azure 機器中執行的應用程式。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2020
ms.author: memildin
ms.openlocfilehash: cbbfddca1a6a07625a69be8ffb0409640d825793
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036939"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>使用適應性應用程式控制來減少電腦的攻擊面

瞭解 Azure 資訊安全中心的彈性應用程式控制的優點，以及如何使用此資料驅動的智慧型功能來增強您的安全性。


## <a name="what-are-security-centers-adaptive-application-controls"></a>什麼是資訊安全中心的彈性應用程式控制？

彈性應用程式控制是一種智慧型且自動化的解決方案，可為您的機器定義已知安全應用程式的允許清單。 

組織通常會有一系列定期執行相同進程的機器。 資訊安全中心使用機器學習服務來分析在您電腦上執行的應用程式，並建立已知安全軟體的清單。 允許清單是以您特定的 Azure 工作負載為基礎，您可以使用下列指示進一步自訂建議。

當您已啟用並設定彈性應用程式控制時，如果任何應用程式執行的不是您定義為安全的，您就會收到安全性警示。


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>彈性應用程式控制的優點為何？

藉由定義已知安全應用程式的清單，並在任何其他專案執行時產生警示，您可以達成多個強化目標：

- 識別潛在惡意程式碼，甚至是反惡意程式碼解決方案可能遺漏的惡意程式碼
- 使用規定僅授權軟體的本機安全性原則來改善合規性
- 避免執行舊版或不支援的應用程式
- 防止貴組織所禁止的特定軟體
- 增加存取敏感性資料之應用程式的監督



## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|發行狀態：|正式運作|
|標價|標準層|
|支援的電腦：|![是 ](./media/icons/yes-icon.png) azure，而非 azure 機器執行 Windows 和 Linux<br>![是 ](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/)機器|
|必要的角色和許可權：|「**安全性讀取**者」和「**讀者**」角色可以同時查看群組和已知安全應用程式的清單<br>「**參與者**」和「**安全性管理員**」角色可以同時編輯群組和已知安全應用程式的清單|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov) |
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>啟用電腦群組上的應用程式控制項

如果資訊安全中心已識別訂用帳戶中一致執行一組類似應用程式的機器群組，系統將會提示您提供下列建議：您**應該在電腦上啟用用於定義安全應用程式的彈性應用程式控制項**。

選取建議，或開啟 [彈性應用程式控制] 頁面，以查看建議的已知安全應用程式和電腦群組的清單。

1. 從資訊安全中心的功能表中，選取 [彈性**應用**程式控制]。

    [彈性**應用**程式控制] 頁面隨即開啟，並將您的 vm 分組為下列索引標籤：

    - 已**設定**-已定義允許的應用程式清單的電腦群組。 針對每個群組，[已設定] 索引標籤會顯示：
        - 群組中的機器數目
        - 最近的警示

    - **建議**-一致執行相同應用程式，但未設定允許清單的電腦群組。 我們建議您為這些群組啟用彈性應用程式控制。
    
      > [!TIP]
      > 如果您看到具有前置詞 "REVIEWGROUP" 的組名，則會包含具有部分一致應用程式清單的電腦。 資訊安全中心無法看到模式，但建議您查看此群組，以查看_您_是否可以手動定義一些彈性應用程式控制規則，如[編輯群組的彈性應用程式控制規則](#editing-a-groups-adaptive-application-controls-rule)中所述。
      >
      > 您也可以將機器從此群組移至其他群組，如[將電腦從一個群組移至另一個群組](#move-a-machine-from-one-group-to-another)中所述。

    - **無建議**-沒有已定義允許的應用程式清單，且不支援此功能的電腦。 您的電腦可能會因為下列原因而在此索引標籤中：
      - 缺少 Log Analytics 代理程式
      - Log Analytics 代理程式未傳送事件
      - 這是一台 Windows 電腦，其中包含由 GPO 或本機安全性原則啟用的既有[AppLocker](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker)原則

      > [!TIP]
      > 資訊安全中心需要至少兩周的資料，以定義每個電腦群組的唯一建議。 最近建立或屬於僅限標準層啟用之訂用帳戶的電腦，將會出現在 [**沒有建議**] 索引標籤底下。


1. 開啟 [**建議**] 索引標籤。出現 [建議的允許清單] 的電腦群組。

   ![建議的索引標籤](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. 選取群組。 

1. 若要設定您的新規則，請檢查此 [**設定應用程式控制規則**] 頁面的各個區段和 [內容]，這將對此特定電腦群組而言是唯一的：

   ![設定新的規則](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **選取機器**-根據預設，會選取已識別群組中的所有電腦。 取消選取任何專案，即可從這個規則中移除它們。
   
   1. **建議的應用程式**-請參閱此群組中電腦通用的此應用程式清單，並建議允許執行。
   
   1. **更多應用程式**-請參閱這份清單，這些應用程式在此群組中的機器上不常出現，或已知為可利用攻擊。 警告圖示表示攻擊者可能會使用特定應用程式來略過應用程式允許清單。 我們建議您仔細檢查這些應用程式。

      > [!TIP]
      > 這兩個應用程式清單都包含將特定應用程式限制為特定使用者的選項。 盡可能採用最低許可權的原則。
      > 
      > 應用程式是由其發行者定義，如果應用程式沒有發行者資訊 (不帶正負號的) ，則會針對特定應用程式的完整路徑建立路徑規則。

   1. 若要套用規則，請選取 [ **Audit**]。 




## <a name="editing-a-groups-adaptive-application-controls-rule"></a>編輯群組的彈性應用程式控制規則

您可能會決定編輯電腦群組的允許清單，因為貴組織有已知的變更。 

若要編輯電腦群組的規則：

1. 從資訊安全中心的功能表中，選取 [彈性**應用**程式控制]。

1. 從 [**已設定**] 索引標籤中，選取您要編輯規則的群組。

1. 如在[機器群組上啟用](#enable-application-controls-on-a-group-of-machines)自動調整應用程式控制中所述，參閱**設定應用程式控制規則**頁面的各個區段。

1. （選擇性）新增一或多個自訂規則：

   1. 選取 [**新增規則**]。

      ![新增自訂規則](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. 如果您要定義已知的安全路徑，請將**規則類型**變更為 [路徑]。 您可以在路徑中包含萬用字元。
   
      > [!TIP]
      > 在路徑中使用萬用字元的部分案例可能會很有用：
      > 
      > * 在路徑結尾使用萬用字元，以允許在此資料夾和子資料夾內的所有可執行檔。
      > * 在路徑的中間使用萬用字元來啟用具有變更資料夾名稱的已知可執行檔名稱 (例如，包含已知可執行檔的個人使用者資料夾、自動產生的資料夾名稱等等) 。
  
   1. 定義允許的使用者和受保護的檔案類型。

   1. 當您完成定義規則時，請選取 [**新增**]。

1. 若要套用變更，請選取 [**儲存**]。




## <a name="responding-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>回應「您的適應性應用程式控制原則中的允許清單規則應該更新」的建議

當資訊安全中心的機器學習服務發現先前未允許的可能合法行為時，您會看到這項建議。 建議建議您現有定義的新規則，以減少錯誤正面警示的數目。

若要修復問題：

1. 在 [建議] 頁面上，選取 [彈性**應用程式控制原則] 中的允許清單規則應該更新**建議，以查看具有新識別之可能合法行為的群組。

1. 選取您想要編輯規則的群組。

1. 如在[機器群組上啟用](#enable-application-controls-on-a-group-of-machines)自動調整應用程式控制中所述，參閱**設定應用程式控制規則**頁面的各個區段。

1. 若要套用變更，請選取 [ **Audit**]。




## <a name="auditing-alerts-and-violations"></a>審核警示和違規

1. 從資訊安全中心的功能表中，選取 [彈性**應用**程式控制]。

1. 若要查看具有最近警示之電腦的群組，請查看 [**已設定**] 索引標籤中所列的群組。

1. 若要進一步調查，請選取群組。

   ![最近的警示](./media/security-center-adaptive-application/recent-alerts.png)

1. 如需進一步的詳細資料，以及受影響的機器清單，請選取警示。



## <a name="move-a-machine-from-one-group-to-another"></a>將電腦從一個群組移至另一個

當您將電腦從一個群組移到另一個時，套用至它的應用程式控制原則會變更為您將它移到的群組設定。 您也可以將電腦從已設定的群組移至未設定的群組，這樣做會移除已套用至電腦的任何應用程式控制規則。

1. 從 [彈性**應用程式控制項**] 頁面的 [**已設定**] 索引標籤中，選取包含要移動之電腦的群組。

1. 開啟**已設定的電腦**清單。

1. 從資料列結尾的三個點開啟電腦的功能表，然後選取 [**移動**]。 [**將電腦移至不同的群組**] 窗格隨即開啟。

1. 選取目的地群組，然後選取 [**移動電腦**]。

1. 若要儲存您的變更，請選取 [**儲存**]。





## <a name="managing-application-controls-via-the-rest-api"></a>透過 REST API 管理應用程式控制 

若要以程式設計方式管理您的彈性應用程式控制，請使用我們的 REST API。 

完整的 API 檔位於[這裡](https://docs.microsoft.com/rest/api/securitycenter/adaptiveapplicationcontrols)。

REST API 提供的部分功能如下：

* **清單**會抓取您所有的群組建議，並為每個群組提供具有物件的 JSON。

* **Get**會使用完整的建議資料來抓取 JSON (也就是電腦清單、發行者/路徑規則等等) 。

* **Put**會設定您的規則 (使用以**Get**作為此要求) 主體的 JSON。
 
   > [!IMPORTANT]
   > **Put**函式所預期的參數比 Get 命令所傳回的 JSON 少。
   >
   > 在 Put 要求中使用 JSON 之前，請先移除下列屬性： recommendationStatus、configurationStatus、問題、位置和及 sourcesystem。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何在 Azure 資訊安全中心中使用調適型應用程式控制，以定義在您的 Azure 和非 Azure 機器上執行之應用程式的允許清單。 若要深入瞭解資訊安全中心其他雲端工作負載保護功能的詳細資訊，請參閱：

* [瞭解及時 (JIT) VM 存取](just-in-time-explained.md)
* [保護您的 Azure Kubernetes 叢集](azure-kubernetes-service-integration.md)