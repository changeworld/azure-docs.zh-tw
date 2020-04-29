---
title: Azure 資訊安全中心中的安全分數
description: Azure 資訊安全中心的安全分數和其安全性控制的描述
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: d7eea9cd83e72b6ffeaae319a8e87c065015e6b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415807"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Azure 資訊安全中心中增強的安全分數（預覽）

## <a name="introduction-to-secure-score"></a>安全分數簡介

Azure 資訊安全中心有兩個主要目標：協助您瞭解目前的安全性情況，並協助您有效率且有效地改善安全性。 可讓您達成這些目標的資訊安全中心中央層面是安全分數。

資訊安全中心持續評估資源、訂用帳戶和組織的安全性問題。 接著，它會將所有的發現匯總成一個分數，讓您可以立即得知目前的安全性情況：分數越高，識別的風險層級越低。 使用分數來追蹤組織中的安全性工作和專案。 

資訊安全中心的 [安全分數] 頁面包括：

- **分數**-安全分數會顯示為百分比值，但基礎值也很明顯：

    [![以百分比值顯示的安全分數，其基礎數位也會清除](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **安全性控制項**-每個控制項都是相關安全性建議的邏輯群組，而且會反映您的易受攻擊面。 控制項是一組安全性建議，其中包含可協助您執行這些建議的指示。 當您針對控制項內的單一資源補救*所有*建議時，您的分數才會改善。

    若要立即查看您的組織如何保護每個個別的受攻擊面，請參閱每個安全性控制的分數。

    如需詳細資訊，請參閱下面[的「安全分數」的計算方式](secure-score-security-controls.md#how-the-secure-score-is-calculated)。 


>[!TIP]
> 較早版本的資訊安全中心在建議層級獲得的點數：當您補救單一資源的建議時，您的安全分數已有所改善。 目前，只有在您修復控制項內單一資源的*所有*建議時，才會改善您的分數。 因此，當您改善資源的安全性時，您的分數只會改善。
> 雖然此增強版本仍處於預覽狀態，但先前的安全分數體驗可作為 Azure 入口網站的選項。 


## <a name="locating-your-secure-score"></a>找出您的安全分數

資訊安全中心會醒目顯示您的分數：這是 [總覽] 頁面中顯示的第一件事。 如果您按一下 [專用安全分數] 頁面，您會看到依訂用帳戶細分的分數。 按一下單一訂用帳戶，以查看優先順序建議的詳細清單，以及補救它們對訂用帳戶分數的潛在影響。

## <a name="how-the-secure-score-is-calculated"></a>如何計算安全分數 

每個安全性控制對整體安全分數的貢獻，會清楚地顯示在 [建議] 頁面上。

[![增強的安全分數引進了安全性控制](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

若要取得安全性控制的所有可能點，您所有的資源都必須符合安全性控制措施中的所有安全性建議。 例如，資訊安全中心有多個關於如何保護管理埠的建議。 在過去，您可以補救一些相關和相互依存的建議，同時讓其他人 unsolved，而您的安全分數也會改善。 在客觀的情況下，您可以輕鬆地認為您的安全性已經過改善，直到全部解決為止。 現在，您必須補救所有專案，以與您的安全分數產生差異。

例如，名為「套用系統更新」的安全性控制項的分數上限為六個點，您可以在工具提示中看到控制項的可能增加值：

[![安全性控制「套用系統更新」](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

上述螢幕擷取畫面中的「套用系統更新」安全性控制可能會顯示「2% （1點）」。 這表示，如果您補救此控制項中的所有建議，您的分數會增加2% （在此案例中為一個點）。 為了簡單起見，建議清單中 [可能增加] 資料行的值會舍入為整數。 工具提示會顯示精確的值：

* **最大分數**-完成控制項內的所有建議可獲得的點數上限。 控制項的最大分數表示該控制項的相對重要性。 使用 [最大分數] 值來分級要優先處理的問題。 
* **可能的增加**-您可以在控制項內使用的剩餘點數。 若要將這些點新增至您的安全分數，請修復所有控制項的建議。 在上述範例中，控制項所顯示的一個點實際上是0.96 點。
* **目前分數**-此控制項的目前分數。 每個控制項都有貢獻總分數。 在此範例中，控制項會對總計貢獻5.04 點。 

### <a name="calculations---understanding-your-score"></a>計算-瞭解您的分數

|計量|公式和範例|
|-|-|
|**安全性控制項的目前分數**|<br>![用於計算安全性控制項目前分數的方程式](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>每個個別的安全性控制都會貢獻安全性分數。 受此控制項中的建議影響的每個資源，都是針對控制項的目前分數。 每個控制項的目前分數是控制項*內*資源狀態的量值。<br>![顯示計算安全性控制項目前分數時使用之值的工具提示](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此範例中，最大分數6會除以78，因為這是狀況良好和狀況不良資源的總和。<br>6/78 = 0.0769<br>乘以狀況良好的資源數目（4）會導致目前的分數：<br>0.0769 * 4 = **0.31**<br><br>|
|**安全分數**<br>單一訂用帳戶|<br>![用於計算目前安全分數的方程式](media/secure-score-security-controls/secure-score-equation.png)<br><br>![已啟用所有控制項的單一訂用帳戶安全分數](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此範例中，會有單一訂用帳戶，其中包含所有可用的安全性控制項（可能的最大分數為60點）。 分數顯示可能60的28點，其餘的32點會反映在安全性控制項的「潛在分數增加」圖中。<br>![控制項清單和可能的分數增加](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全分數**<br>多重訂閱|<br>新增所有訂用帳戶中所有資源的目前分數，然後計算會與單一訂用帳戶相同<br><br>在查看多個訂用帳戶時，安全分數會評估所有已啟用原則中的所有資源，並將其對每個安全性控制的最大分數所造成的影響<br>![已啟用所有控制項的多個訂用帳戶的安全分數](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>結合的分數**不**是平均值;而是所有訂用帳戶上所有資源狀態的評估狀態。<br>在這裡，如果您移至 [建議] 頁面並加上可能的可用點，您會發現這是目前分數（24）和可用的最大分數（60）之間的差異。|
||||

## <a name="improving-your-secure-score"></a>提升您的安全分數

若要改善您的安全分數，請從建議清單中補救安全性建議。 您可以針對每個資源，或使用**快速修正**來手動補救每個建議！ 選項（如果有的話）會快速將建議的補救套用至資源群組。 如需詳細資訊，請參閱[補救建議](security-center-remediate-recommendations.md)。

>[!IMPORTANT]
> 只有內建建議會影響安全分數。

## <a name="security-controls-and-their-recommendations"></a>安全性控制項及其建議

下表列出 Azure 資訊安全中心中的安全性控制項。 針對每個控制項，如果您針對*所有*資源補救控制項中列出的*所有*建議，您可以看到可新增至安全分數的最大點數。 

> [!TIP]
> 如果您想要以不同的方式篩選或排序這份清單，請將其複製並貼到 Excel 中。

|安全性控制|最大安全分數點|建議|
|----------------|:-------------------:|---------------|
|**啟用 MFA**|10|-應在訂用帳戶上具有擁有者許可權的帳戶上啟用 MFA<br>-MFA 應在訂用帳戶上具有讀取權限的帳戶上啟用<br>-MFA 應為您訂用帳戶上具有寫入權限的帳戶啟用|
|**保護管理埠**|8|-即時網路存取控制應套用在虛擬機器上<br>-虛擬機器應該與網路安全性群組相關聯<br>-管理埠應在您的虛擬機器上關閉|
|**套用系統更新**|6|-監視代理程式健康情況問題應在您的電腦上解決<br>-監視代理程式應該安裝在虛擬機器擴展集上<br>-監視代理程式應該安裝在您的電腦上<br>-應針對您的雲端服務角色更新 OS 版本<br>-應安裝虛擬機器擴展集上的系統更新<br>-系統更新應該安裝在您的電腦上<br>-應重新開機您的電腦以套用系統更新<br>-Kubernetes 服務應該升級為不容易受攻擊的 Kubernetes 版本<br>-監視代理程式應該安裝在您的虛擬機器上|
|**修復弱點**|6|-應在您的 SQL server 上啟用 Advanced data security<br>-應補救 Azure Container Registry 映射中的弱點<br>-應補救 SQL 資料庫上的弱點<br>-弱點評估解決方案應補救弱點<br>-應在您的 SQL 受控實例上啟用弱點評估<br>-應在您的 SQL server 上啟用弱點評估<br>-弱點評估解決方案應該安裝在您的虛擬機器上|
|**啟用待用加密**|4|-應在虛擬機器上套用磁片加密<br>-應啟用 SQL 資料庫上的透明資料加密<br>-自動化帳戶變數應加密<br>-Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign<br>-應使用您自己的金鑰來加密 SQL server TDE 保護裝置|
|**加密傳輸中的資料**|4|-API 應用程式應該只能透過 HTTPS 存取<br>-函數應用程式只能透過 HTTPS 存取<br>-只應啟用 Redis 快取的安全連線<br>-應啟用儲存體帳戶的安全傳輸<br>-Web 應用程式應該只能透過 HTTPS 存取|
|**管理存取權和許可權**|4|-應為您的訂用帳戶指定最多3位擁有者<br>-應從您的訂用帳戶移除已淘汰的帳戶（預覽）<br>-應從您的訂用帳戶移除具有擁有者許可權的已淘汰帳戶（預覽）<br>-應從您的訂用帳戶中移除具有擁有者許可權的外部帳戶（預覽）<br>-應從您的訂用帳戶移除具有讀取權限的外部帳戶<br>-應從您的訂用帳戶中移除具有寫入權限的外部帳戶（預覽）<br>-應將一個以上的擁有者指派給您的訂用帳戶<br>-以角色為基礎的存取控制（RBAC）應用於 Kubernetes 服務（預覽）<br>-Service Fabric 叢集應該只使用 Azure Active Directory 進行用戶端驗證|
|**補救安全性設定**|4|-Pod 安全性原則應定義于 Kubernetes 服務上<br>-應補救容器安全性設定中的弱點<br>-應補救您電腦上安全性設定中的弱點<br>-應補救虛擬機器擴展集上安全性設定中的弱點<br>-監視代理程式應該安裝在您的虛擬機器上<br>-監視代理程式應該安裝在您的電腦上<br>-監視代理程式應該安裝在虛擬機器擴展集上<br>-監視代理程式健康情況問題應在您的電腦上解決|
|**限制未經授權的網路存取**|4|-您的虛擬機器上的 IP 轉送應停用<br>-已授權的 IP 範圍應定義于 Kubernetes Services （預覽）<br>-（已淘汰）應限制應用程式服務的存取（預覽）<br>-（已淘汰）應強化 IaaS Nsg 上 web 應用程式的規則<br>-虛擬機器應該與網路安全性群組相關聯<br>-CORS 不應允許每個資源存取您的 API 應用程式<br>-CORS 不應允許每個資源存取您的函數應用程式<br>-CORS 不應允許每個資源存取您的 Web 應用程式<br>-API 應用程式的遠端偵測應關閉<br>-應關閉函數應用程式的遠端偵錯程式<br>-應關閉 Web 應用程式的遠端偵錯程式<br>-應限制與網際網路對向 Vm 的寬鬆網路安全性群組<br>-應強化網際網路面向虛擬機器的網路安全性群組規則|
|**套用適應性應用程式控制**|3|-應在虛擬機器上啟用彈性應用程式控制<br>-監視代理程式應該安裝在您的虛擬機器上<br>-監視代理程式應該安裝在您的電腦上<br>-監視代理程式健康情況問題應在您的電腦上解決|
|**套用資料分類**|2|-SQL 資料庫中的機密資料應分類（預覽）|
|**保護應用程式免于遭受 DDoS 攻擊**|2|-應啟用 DDoS 保護標準|
|**啟用 endpoint protection**|2|-應在虛擬機器擴展集上補救端點保護健康情況失敗<br>-應在您的電腦上解決端點保護健康情況問題<br>-端點保護解決方案應該安裝在虛擬機器擴展集上<br>-在虛擬機器上安裝端點保護解決方案<br>-監視代理程式健康情況問題應在您的電腦上解決<br>-監視代理程式應該安裝在虛擬機器擴展集上<br>-監視代理程式應該安裝在您的電腦上<br>-監視代理程式應該安裝在您的虛擬機器上<br>-在您的電腦上安裝 endpoint protection 解決方案|
|**啟用審核和記錄**|1|-應啟用 SQL server 上的審核<br>-應啟用應用程式服務中的診斷記錄<br>-應啟用 Azure Data Lake 存放區中的診斷記錄<br>-應啟用 Azure 串流分析中的診斷記錄<br>-應啟用 Batch 帳戶中的診斷記錄<br>-應啟用 Data Lake Analytics 中的診斷記錄<br>-應啟用事件中樞的診斷記錄<br>-應啟用 IoT 中樞中的診斷記錄<br>-應啟用 Key Vault 中的診斷記錄<br>-應啟用 Logic Apps 中的診斷記錄<br>-搜尋服務中的診斷記錄應該啟用<br>-應啟用服務匯流排中的診斷記錄<br>-應啟用虛擬機器擴展集中的診斷記錄<br>-應設定 Batch 帳戶上的計量警示規則<br>-SQL 審核設定應設定動作群組來捕獲重要活動<br>-SQL server 應設定為超過90天的審核保留天數。|
|**實行安全性最佳做法**|0|-應限制具有防火牆和虛擬網路設定的儲存體帳戶存取權<br>-應該從事件中樞命名空間移除 RootManageSharedAccessKey 以外的所有授權規則<br>-應針對 SQL server 布建 Azure Active Directory 系統管理員<br>-應定義事件中樞實例上的授權規則<br>-儲存體帳戶應遷移至新的 Azure Resource Manager 資源<br>-虛擬機器應該遷移至新的 Azure Resource Manager 資源<br>-SQL server 的 Advanced data security 設定應該包含用來接收安全性警示的電子郵件地址<br>-您應該在受控實例上啟用 Advanced data security<br>-所有先進的威脅防護類型都應該在 SQL 受控實例的 advanced data security 設定中啟用<br>-系統管理員和訂用帳戶擁有者的電子郵件通知應該在 SQL server advanced data security 設定中啟用<br>-SQL server Advanced Data Security 設定中的 [先進的威脅防護類型] 應該設定為 [全部]<br>-子網應與網路安全性群組相關聯<br>-所有先進的威脅防護類型都應該在 SQL server advanced data security 設定中啟用|
||||

## <a name="secure-score-faq"></a>安全分數常見問題

### <a name="why-has-my-secure-score-gone-down"></a>為什麼我的安全分數已經關閉？
資訊安全中心已切換為增強的安全分數（目前處於預覽狀態），其中包括計算分數方式的變更。 現在，您必須解決資源對接收點的所有建議。 分數也會變更為0-10 的比例。

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果我只在安全性控制中處理四個建議中的三個，我的安全分數是否會改變？
否。 在您修復單一資源的所有建議之前，它不會變更。 若要取得控制項的最大分數，您必須針對所有資源補救所有建議。

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>先前的安全分數使用經驗是否仍然可用？ 
是。 一段時間，它們會並存執行以減輕轉換。 預期先前的模型會以時間為階段。 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果建議不適用於我，而我在原則中將它停用，我的安全性控制是否會完成，而且我的安全分數也會更新？
是。 建議您在環境中不適用時停用建議。 如需有關如何停用特定建議的指示，請參閱[停用安全性原則](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果安全性控制提供給我安全分數的零點，我應該忽略它嗎？
在某些情況下，您會看到控制項的最大分數大於零，但影響是零。 當修正資源的累加分數可以忽略時，會四捨五入為零。 請勿忽略這些建議，因為它們仍然會帶來安全性改進。 唯一的例外是「其他最佳作法」控制項。 補救這些建議並不會增加您的分數，但它會增強您的整體安全性。

## <a name="next-steps"></a>後續步驟

本文說明安全分數和其引進的安全性控制。 如需相關內容，請參閱下列文章：

- [瞭解建議的不同元素](security-center-recommendations.md)
- [瞭解如何修復建議](security-center-remediate-recommendations.md)