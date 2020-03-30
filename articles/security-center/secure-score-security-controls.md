---
title: Azure 安全中心的安全分數
description: Azure 安全中心安全得分及其安全控制的描述
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415807"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Azure 安全中心增強的安全分數（預覽）

## <a name="introduction-to-secure-score"></a>安全分數簡介

Azure 安全中心有兩個主要目標：説明您瞭解當前的安全狀況，並説明您高效有效地提高安全性。 安全中心的核心方面是安全得分，使您能夠實現這些目標。

安全中心持續評估您的資源、訂閱和組織安全問題。 然後，它將所有發現聚合到單個分數中，以便您可以一目了然地判斷您當前的安全情況：分數越高，識別的風險級別越低。 使用分數跟蹤組織中的安全工作和專案。 

安全中心的安全分數頁包括：

- **分數**- 安全分數顯示為百分比值，但基礎值也很清楚：

    [![安全分數顯示為百分比值，基礎數位也清晰](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **安全控制**- 每個控制項都是一組相關的安全建議，並反映易受攻擊的攻擊面。 控制項是一組安全建議，其中包含可説明您實現這些建議的說明。 僅當修復控制項中單個資源*的所有*建議時，您的分數才會提高。

    要立即查看您的組織保護每個攻擊面的能力，請查看每個安全控制的分數。

    有關詳細資訊，請參閱下面[如何計算安全分數](secure-score-security-controls.md#how-the-secure-score-is-calculated)。 


>[!TIP]
> 早期版本的安全中心在推薦級別授予分數：當您對單個資源進行修正時，您的安全分數會提高。 如今，只有修復控制項中單個資源*的所有*建議，您的分數才會提高。 因此，只有在提高資源的安全性時，您的分數才會提高。
> 雖然此增強版本仍處於預覽階段，但早期安全分數體驗可從 Azure 門戶作為選項提供。 


## <a name="locating-your-secure-score"></a>找到您的安全分數

安全中心顯著顯示您的分數：這是"概述"頁中顯示的第一件事。 如果您按一下專用安全分數頁，您將看到按訂閱細分的分數。 按一下單個訂閱以查看優先建議的詳細清單以及修復這些建議對訂閱分數的潛在影響。

## <a name="how-the-secure-score-is-calculated"></a>如何計算安全分數 

建議頁面上清楚地顯示了每個安全控制對總體安全分數的貢獻。

[![增強的安全分數引入了安全控制](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

要獲取安全控制的所有可能點，所有資源都必須符合安全控制中的所有安全建議。 例如，安全中心對如何保護管理埠提出了多項建議。 過去，您可以修復其中一些相關和相互依賴的建議，同時使其他建議未解決，您的安全分數將得到改善。 客觀地看，很容易說，你的安全沒有改善，直到你解決了所有問題。 現在，您必須修復所有，以改變您的安全分數。

例如，稱為"應用系統更新"的安全控制的最大分數為 6 分，您可以在工具提示中看到控制項的潛在增加值：

[![安全控制"應用系統更新"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

上圖中安全控制"應用系統更新"的可能性顯示"2%（1 點）"。 這意味著，如果修復此控制項中的所有建議，您的分數將增加 2%（在這種情況下，一個點）。 為簡單起見，建議清單中的"潛在增加"列中的值四捨五入為整數。 工具提示顯示精確值：

* **最大分數**- 通過在控制項內完成所有建議，您可以獲得的最大分數數。 控制項的最大分數指示該控制項的相對顯著性。 使用最大分數值來分診要首先處理哪些問題。 
* **潛在增加**- 控制項內可供您使用的剩餘點。 要將這些分數添加到安全分數中，請修復控制項的所有建議。 在上面的示例中，控制項顯示的一個點實際上是 0.96 點。
* **當前分數**- 此控制項的當前分數。 每個控制項都會對總分做出貢獻。 在此示例中，控制項對總計貢獻 5.04 點。 

### <a name="calculations---understanding-your-score"></a>計算 - 瞭解您的分數

|計量|公式和示例|
|-|-|
|**安全控制當前得分**|<br>![計算安全控制當前分數的方程](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>每個單獨的安全控制都有助於提高安全分數。 受控制項內建議影響的每個資源都有助於控制當前的得分。 每個控制項的當前分數是控制項*內*資源狀態的度量。<br>![顯示計算安全控制當前分數時使用的值的工具提示](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此示例中，6 的最大值將除以 78，因為這是健康和不正常資源的總和。<br>6 / 78 = 0.0769<br>乘以健康資源數 （4） 可生成當前分數：<br>0.0769 × 4 × **0.31**<br><br>|
|**安全分數**<br>單一訂用帳戶|<br>![用於計算當前安全分數的方程](media/secure-score-security-controls/secure-score-equation.png)<br><br>![啟用所有控制項的單次訂閱安全分數](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此示例中，有一個訂閱，所有安全控制都可用（潛在最高分數為 60 分）。 得分顯示28分的可能60和剩餘的32分反映在"潛在分數增加"的數位的安全控制。<br>![控制項清單和潛在分數增加](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全分數**<br>多重訂閱|<br>添加所有訂閱中所有資源的當前分數，然後計算與單個訂閱相同<br><br>查看多個訂閱時，安全分數會評估所有啟用的策略中的所有資源，並對其進行組合影響，以影響每個安全控制的最大分數。<br>![啟用所有控制項後，為多個訂閱的安全分數](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>合併分數不是平均值;因此，合併分數**不是**平均值。而是評估所有訂閱中所有資源的狀態。<br>在這裡，如果您轉到建議頁面並添加可用的潛在分數，您會發現當前分數 （24） 和可用最大分數 （60） 之間的差異。|
||||

## <a name="improving-your-secure-score"></a>提高您的安全分數

要提高安全分數，請從建議清單中修復安全建議。 您可以為每個資源手動修復每個建議，或者使用**快速修復！** 選項（如果可用）可快速將建議的修正應用於一組資源。 有關詳細資訊，請參閱[修正建議](security-center-remediate-recommendations.md)。

>[!IMPORTANT]
> 只有內置建議才會對安全分數產生影響。

## <a name="security-controls-and-their-recommendations"></a>安全控制及其建議

下表列出了 Azure 安全中心中的安全控制項。 對於每個控制項，如果修復控制項中列出的*所有*資源*的所有*建議，則可以查看可添加到安全分數的最大點數。 

> [!TIP]
> 如果要以不同方式篩選或排序此清單，請將其複製並粘貼到 Excel 中。

|安全控制|最大安全分數|建議|
|----------------|:-------------------:|---------------|
|**啟用 MFA**|10|- 應在具有訂閱擁有者許可權的帳戶上啟用 MFA<br>- 應在訂閱上具有讀取權限的帳戶上啟用 MFA<br>- 應啟用具有訂閱寫入權限的 MFA 帳戶|
|**安全管理埠**|8|- 應在虛擬機器上應用即時網路存取控制<br>- 虛擬機器應與網路安全性群組關聯<br>- 應在虛擬機器上關閉管理埠|
|**套用系統更新**|6|- 監控代理運行狀況問題應在您的電腦上解決<br>- 監視代理應安裝在虛擬機器規模集中<br>- 監控代理應安裝在您的電腦上<br>- 應針對雲服務角色更新作業系統版本<br>- 應安裝虛擬機器規模集的系統更新<br>- 系統更新應安裝在您的電腦上<br>- 應重新開機電腦以應用系統更新<br>- 庫伯內斯服務應升級到非易受攻擊的庫伯內斯版本<br>- 監視代理應安裝在虛擬機器上|
|**修復弱點**|6|- 應在 SQL 伺服器上啟用高級資料安全性<br>- 應修復 Azure 容器註冊表映射中的漏洞<br>- 應修復 SQL 資料庫上的漏洞<br>- 漏洞應通過漏洞評估解決方案進行補救<br>- 應在 SQL 託管實例上啟用漏洞評估<br>- 應在 SQL 伺服器上啟用漏洞評估<br>- 漏洞評估解決方案應安裝在虛擬機器上|
|**在靜止時啟用加密**|4|- 磁片加密應用於虛擬機器<br>- 應啟用 SQL 資料庫上的透明資料加密<br>- 應加密自動化帳戶變數<br>- 服務結構群集應將群集保護層級屬性設置為加密和簽名<br>- SQL 伺服器 TDE 保護器應使用您自己的金鑰進行加密|
|**加密傳輸中的資料**|4|- API 應用程式應僅通過 HTTPS 訪問<br>- 功能應用程式應僅通過 HTTPS 訪問<br>- 應僅啟用與 Redis 緩存的安全連線<br>- 應啟用安全轉移到存儲帳戶<br>- Web 應用程式只能通過 HTTPS 訪問|
|**管理存取權限和許可權**|4|- 應為您的訂閱指定最多 3 個擁有者<br>- 應從訂閱中刪除已棄用的帳戶（預覽版）<br>- 應從訂閱中刪除具有擁有者許可權的已棄用帳戶（預覽版）<br>- 應從訂閱中刪除具有擁有者許可權的外部帳戶（預覽版）<br>- 應從訂閱中刪除具有讀取權限的外部帳戶<br>- 應從訂閱中刪除具有寫入權限的外部帳戶（預覽版）<br>- 應有多個擁有者分配給您的訂閱<br>- 基於角色的存取控制 （RBAC） 應在庫伯奈斯服務（預覽版）上使用<br>- 服務結構群集應僅使用 Azure 活動目錄進行用戶端身份驗證|
|**修復安全配置**|4|- 應在庫伯內斯服務上定義 Pod 安全性原則<br>- 應修復容器安全配置中的漏洞<br>- 應修復電腦安全性配置中的漏洞<br>- 應修復虛擬機器規模集中的安全配置漏洞<br>- 監視代理應安裝在虛擬機器上<br>- 監控代理應安裝在您的電腦上<br>- 監視代理應安裝在虛擬機器規模集中<br>- 監控代理運行狀況問題應在您的電腦上解決|
|**限制未經授權的網路訪問**|4|- 應禁用虛擬機器上的 IP 轉發<br>- 授權 IP 範圍應在庫伯奈斯服務（預覽）上定義<br>- （取消）應用服務訪問應受到限制（預覽）<br>- （解密） IaaS NSG 上的 Web 應用程式規則應強化<br>- 虛擬機器應與網路安全性群組關聯<br>- CORS 不應允許每個資源訪問您的 API 應用<br>- CORS 不應允許每個資源訪問您的功能應用<br>- CORS 不應允許每個資源訪問您的 Web 應用程式<br>- 應關閉 API 應用的遠端偵錯<br>- 應關閉函數應用的遠端偵錯<br>- 應關閉 Web 應用程式的遠端偵錯<br>- 對於具有面向 Internet 的 VM 的允許性網路安全性群組，應限制訪問<br>- 應強化面向互聯網的虛擬機器的網路安全性群組規則|
|**應用自我調整應用程式控制**|3|- 應在虛擬機器上啟用自我調整應用程式控制<br>- 監視代理應安裝在虛擬機器上<br>- 監控代理應安裝在您的電腦上<br>- 監控代理運行狀況問題應在您的電腦上解決|
|**應用資料分類**|2|- 應對 SQL 資料庫中的敏感性資料進行分類（預覽）|
|**保護應用程式免受 DDoS 攻擊**|2|- 應啟用 DDoS 保護標準|
|**啟用端點保護**|2|- 應在虛擬機器規模集中修復端點保護運行狀況故障<br>- 應在電腦上解決端點保護運行狀況問題<br>- 端點保護解決方案應安裝在虛擬機器規模集中<br>- 在虛擬機器上安裝端點保護解決方案<br>- 監控代理運行狀況問題應在您的電腦上解決<br>- 監視代理應安裝在虛擬機器規模集中<br>- 監控代理應安裝在您的電腦上<br>- 監視代理應安裝在虛擬機器上<br>- 在電腦上安裝端點保護解決方案|
|**啟用審核和日誌記錄**|1|- 應啟用 SQL 伺服器上的審核<br>- 應啟用應用服務中的診斷日誌<br>- 應啟用 Azure 資料湖存儲中的診斷日誌<br>- 應啟用 Azure 流分析中的診斷日誌<br>- 應啟用批次處理帳戶中的診斷日誌<br>- 應啟用資料湖分析中的診斷日誌<br>- 應啟用事件中心中的診斷日誌<br>- 應啟用 IoT 中心中的診斷日誌<br>- 應啟用金鑰保存庫中的診斷日誌<br>- 應啟用邏輯應用中的診斷日誌<br>- 應啟用搜索服務中的診斷日誌<br>- 應啟用服務匯流排中的診斷日誌<br>- 應啟用虛擬機器規模集中的診斷日誌<br>- 應在批次處理帳戶上配置指標警報規則<br>- SQL 審核設置應配置操作組以捕獲關鍵活動<br>- SQL 伺服器應配置審核保留天數大於 90 天。|
|**實施安全最佳實踐**|0|- 應限制使用防火牆和虛擬網路配置訪問存儲帳戶<br>- 應從事件中心命名空間中刪除除根管理共用訪問金鑰之外的所有授權規則<br>- 應為 SQL 伺服器預配 Azure 活動目錄管理員<br>- 應定義事件中心實例上的授權規則<br>- 存儲帳戶應遷移到新的 Azure 資源管理器資源<br>- 虛擬機器應遷移到新的 Azure 資源管理器資源<br>- SQL 伺服器的高級資料安全設置應包含電子郵件地址以接收安全警報<br>- 應在託管實例上啟用高級資料安全性<br>- 應在 SQL 託管實例高級資料安全設置中啟用所有高級威脅保護類型<br>- 應在 SQL 伺服器高級資料安全設置中啟用發送給管理員和訂閱擁有者的電子郵件通知<br>- 高級威脅保護類型應在 SQL 伺服器高級資料安全設置中設置為"全部"<br>- 子網應與網路安全性群組關聯<br>- 應在 SQL 伺服器高級資料安全設置中啟用所有高級威脅防護類型|
||||

## <a name="secure-score-faq"></a>安全分數常見問題解答

### <a name="why-has-my-secure-score-gone-down"></a>為什麼我的安全分數下降了？
安全中心已切換到增強的安全分數（當前處於預覽狀態），其中包括分數計算方式的更改。 現在，您必須解決資源接收點的所有建議。 分數也更改為 0-10。

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果我只處理安全控制中四分之三的建議，我的安全分數是否會更改？
否。 在修復單個資源的所有建議之前，它不會更改。 要獲取控制項的最大分數，必須修復所有資源的所有建議。

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>安全分數的先前體驗是否仍然可用？ 
是。 有一段時間，他們將並排運行，以緩解過渡。 預期以前的模型會及時淘汰。 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果建議不適用於我，並且我在策略中禁用了它，我的安全控制是否會得到滿足並更新我的安全分數？
是。 當建議不適用於您的環境時，我們建議禁用這些建議。 有關如何禁用特定建議的說明，請參閱[禁用安全性原則](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果安全控制為我的安全分數提供零點，我應該忽略它嗎？
在某些情況下，您將看到控制項最大值大於零，但影響為零。 當修復資源的增量分數可以忽略不計時，則四捨五入為零。 不要忽視這些建議，因為它們仍然會帶來安全改進。 唯一的例外是"其他最佳實踐"控制項。 修復這些建議不會提高您的分數，但它會提高您的整體安全性。

## <a name="next-steps"></a>後續步驟

本文介紹了安全分數及其引入的安全控制。 有關相關材料，請參閱以下文章：

- [瞭解建議的不同元素](security-center-recommendations.md)
- [瞭解如何修復建議](security-center-remediate-recommendations.md)