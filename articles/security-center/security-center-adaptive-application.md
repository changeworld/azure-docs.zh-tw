---
title: Azure 資訊安全中心的自適性應用程式控制
description: 本檔可協助您在 Azure 資訊安全中心中使用自動調整應用程式控制，以允許在 Azure 機器上執行的清單應用程式。
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
ms.openlocfilehash: d4ed88dea0d230fd3488621c217ef9ea086310d5
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691474"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>使用適應性應用程式控制來減少電腦的攻擊面

瞭解 Azure 資訊安全中心的調適型應用程式控制的優點，以及如何利用此資料驅動的智慧型功能來增強您的安全性。


## <a name="what-are-security-centers-adaptive-application-controls"></a>什麼是資訊安全中心的自我調整應用程式控制？

自動調整應用程式控制項是智慧型和自動化的解決方案，可為您的機器定義已知安全應用程式的允許清單。 

通常，組織會有定期執行相同進程的電腦集合。 資訊安全中心會使用機器學習來分析電腦上執行的應用程式，並建立已知安全的軟體清單。 允許清單是以您特定的 Azure 工作負載為基礎，您可以使用下列指示進一步自訂建議。

當您已啟用並設定彈性應用程式控制之後，如果任何應用程式執行的應用程式不是您定義為安全的應用程式，您將會收到安全性警示。


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>適應性應用程式控制的優點為何？

藉由定義已知安全的應用程式清單，並在執行任何其他動作時產生警示，您可以達成多重強化目標：

- 找出惡意程式碼解決方案可能遺漏的潛在惡意程式碼
- 使用規定僅限授權軟體的本機安全性原則來改善合規性
- 避免執行舊的或不支援的應用程式
- 防止您的組織禁止的特定軟體
- 提高存取敏感性資料的應用程式監督



## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式運作|
|定價：|標準層|
|支援的電腦：|![是 ](./media/icons/yes-icon.png) azure 和不是執行 Windows 和 Linux 的非 azure 機器<br>![是 ](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) 電腦|
|必要的角色和許可權：|**安全性讀取** 者和 **讀取** 者角色可以同時查看群組和已知安全的應用程式清單<br>**參與者** 和 **安全性系統管理員** 角色可以編輯群組和已知安全的應用程式清單|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>在一組電腦上啟用應用程式控制

如果資訊安全中心在您的訂用帳戶中識別出的機器群組一致地執行一組類似的應用程式，系統將會提示您輸入下列建議：您 **應在您的電腦上啟用定義安全應用程式的彈性應用**程式控制。

選取建議，或開啟 [自動調整應用程式控制] 頁面，以查看建議的已知安全應用程式和電腦群組的清單。

1. 從 [安全性中心] 的功能表中，選取 [ **自我調整應用**程式控制]。

    [自動調整 **應用** 程式控制] 頁面隨即開啟，並將您的 vm 分組為下列索引標籤：

    - 已**設定**-已定義應用程式允許清單的電腦群組。 針對每個群組，[已設定] 索引標籤會顯示：
        - 群組中的電腦數目
        - 最近的警示

    - **建議** -一致執行相同應用程式且未設定允許清單的電腦群組。 建議您為這些群組啟用適應性應用程式控制。
    
      > [!TIP]
      > 如果您看到名為 "REVIEWGROUP" 的組名，它會包含具有部分一致性應用程式清單的電腦。 安全性中心無法看到模式，但建議您檢查此群組以查看 _您_ 是否可以手動定義某些自動調整應用程式控制規則，如 [編輯群組的自我調整應用程式控制規則](#editing-a-groups-adaptive-application-controls-rule)中所述。
      >
      > 您也可以將機器從此群組移至其他群組，如 [將機器從某個群組移至另一個群組](#move-a-machine-from-one-group-to-another)所述。

    - **沒有任何建議** -沒有已定義的允許清單應用程式的電腦，且不支援此功能。 您的電腦可能會在此索引標籤中，原因如下：
      - 缺少 Log Analytics 代理程式
      - Log Analytics 代理程式未傳送事件
      - 它是具有已由 GPO 或本機安全性原則啟用之既存 [AppLocker](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) 原則的 Windows 電腦

      > [!TIP]
      > 安全性中心需要至少兩周的資料，以定義每個電腦群組的唯一建議。 最近建立的電腦，或屬於最近使用標準層啟用之訂用帳戶的電腦，將會出現在 [ **沒有建議** ] 索引標籤底下。


1. 開啟 [ **建議** ] 索引標籤。具有建議允許清單的電腦群組隨即出現。

   ![建議索引標籤](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. 選取群組。 

1. 若要設定您的新規則，請檢查 [ **設定應用程式控制規則** ] 頁面的各個區段和內容，這對此特定電腦群組而言是唯一的：

   ![設定新規則](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **選取電腦** -根據預設，系統會選取已識別群組中的所有電腦。 取消選取任何專案，將其從此規則中移除。
   
   1. **建議的應用程式** -請參閱此群組中電腦通用的應用程式清單，並建議您允許執行。
   
   1. **更多應用程式** -在此群組中的電腦上查看較少的應用程式清單，或已知可進行攻擊的應用程式清單。 警告圖示表示攻擊者可以使用特定應用程式來略過應用程式允許清單。 建議您仔細複習這些應用程式。

      > [!TIP]
      > 這兩個應用程式清單都包含將特定應用程式限制在特定使用者的選項。 盡可能採用最低許可權原則。
      > 
      > 應用程式是由其發行者定義，如果應用程式沒有發行者資訊 (不帶正負號的) ，則會針對特定應用程式的完整路徑建立路徑規則。

   1. 若要套用規則，請選取 [ **Audit**]。 




## <a name="editing-a-groups-adaptive-application-controls-rule"></a>編輯群組的自我調整應用程式控制規則

您可能會因為組織已知的變更，而決定編輯一組電腦的允許清單。 

若要編輯一組電腦的規則：

1. 從 [安全性中心] 的功能表中，選取 [ **自我調整應用**程式控制]。

1. 在 [ **已設定** ] 索引標籤中，選取包含您要編輯之規則的群組。

1. 請參閱 **設定應用程式控制規則** 頁面的各個區段，如在 [一組電腦上啟用](#enable-application-controls-on-a-group-of-machines)自動調整應用程式控制項所述。

1. （選擇性）新增一或多個自訂規則：

   1. 選取 [ **新增規則**]。

      ![新增自訂規則](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. 如果您要定義已知的安全路徑，請將 **規則類型** 變更為「路徑」。 您可以在路徑中包含萬用字元。
   
      > [!TIP]
      > 在路徑中使用萬用字元的某些案例可能很有用：
      > 
      > * 在路徑結尾使用萬用字元，以允許此資料夾和子資料夾內的所有可執行檔。
      > * 在路徑中間使用萬用字元來啟用具有變更資料夾名稱的已知可執行檔名稱 (例如，包含已知可執行檔的個人使用者資料夾、自動產生的資料夾名稱等) 。
  
   1. 定義允許的使用者和受保護的檔案類型。

   1. 當您完成定義規則之後，請選取 [ **新增**]。

1. 若要套用變更，請選取 [ **儲存**]。




## <a name="responding-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>回應「您的適應性應用程式控制原則中的允許清單規則應該更新」的建議

當安全性中心的機器學習找出先前未允許的合法行為時，您將會看到這種建議。 建議建議您現有定義的新規則，以減少誤報的誤報數目。

若要修復問題：

1. 在 [建議] 頁面中，選取 **您的適應性應用程式控制原則中的允許清單規則應該更新** 建議，以查看具有新識別、可能合法行為的群組。

1. 選取具有您想要編輯之規則的群組。

1. 請參閱 **設定應用程式控制規則** 頁面的各個區段，如在 [一組電腦上啟用](#enable-application-controls-on-a-group-of-machines)自動調整應用程式控制項所述。

1. 若要套用變更，請選取 [ **Audit**]。




## <a name="auditing-alerts-and-violations"></a>審核警示和違規

1. 從 [安全性中心] 的功能表中，選取 [ **自我調整應用**程式控制]。

1. 若要查看具有最新警示的電腦群組，請檢查 [ **已設定** ] 索引標籤中列出的群組。

1. 若要進一步調查，請選取群組。

   ![最近的警示](./media/security-center-adaptive-application/recent-alerts.png)

1. 如需詳細資訊和受影響機器的清單，請選取警示。



## <a name="move-a-machine-from-one-group-to-another"></a>將機器從一個群組移至另一個群組

當您將機器從某個群組移至另一個群組時，套用至它的應用程式控制原則會變更至您移至的群組設定。 您也可以將電腦從已設定的群組移至未設定的群組，這樣做會移除套用至電腦的任何應用程式控制規則。

1. 從 [ **自我調整應用** 程式控制] 頁面的 [ **已設定** ] 索引標籤中，選取包含要移動之電腦的群組。

1. 開啟  **已設定的電腦**清單。

1. 從資料列結尾的三個點開啟電腦的功能表，然後選取 [ **移動**]。 [ **將電腦移到不同的群組** ] 窗格隨即開啟。

1. 選取目的地群組，然後選取 [ **移動電腦**]。

1. 若要儲存變更，請選取 [ **儲存**]。





## <a name="managing-application-controls-via-the-rest-api"></a>透過 REST API 管理應用程式控制 

若要以程式設計方式管理您的自我調整應用程式控制，請使用我們的 REST API。 

完整的 API 檔位於 [此處](https://docs.microsoft.com/rest/api/securitycenter/adaptiveapplicationcontrols)。

REST API 中提供的部分函數：

* **清單** 會抓取您所有群組的建議，並為每個群組提供具有物件的 JSON。

* **取得** 具有完整建議資料的 JSON (也就是電腦清單、發行者/路徑規則等等) 。

* **Put** 會設定您的規則 (使用您 **抓取的 JSON 作為此** 要求) 的主體。
 
   > [!IMPORTANT]
   > **Put**函式需要的參數少於 Get 命令所傳回的 JSON。
   >
   > 在 Put 要求中使用 JSON 之前，請先移除下列屬性： recommendationStatus、configurationStatus、問題、位置和 sourceSystem。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何在 Azure 資訊安全中心中使用自動調整應用程式控制，以定義在 Azure 和非 Azure 電腦上執行之應用程式的允許清單。 若要深入瞭解資訊安全中心的其他雲端工作負載保護功能，請參閱：

* [瞭解 (JIT) VM 存取的即時](just-in-time-explained.md)
* [保護您的 Azure Kubernetes 叢集](azure-kubernetes-service-integration.md)