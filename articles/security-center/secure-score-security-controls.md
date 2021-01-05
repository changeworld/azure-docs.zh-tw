---
title: Azure 資訊安全中心的安全分數
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
ms.date: 11/10/2020
ms.author: memildin
ms.openlocfilehash: 283dbf152778b5ef58e298444058f619356ef241
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756104"
---
# <a name="secure-score-in-azure-security-center"></a>Azure 資訊安全中心的安全分數

## <a name="introduction-to-secure-score"></a>安全分數簡介

Azure 資訊安全中心有兩個主要目標： 

- 協助您瞭解目前的安全性狀況
- 協助您有效率且有效地改善安全性

安全中心的集中功能，可讓您達成這些目標的 **安全分數**。

資訊安全中心會持續評估資源、訂用帳戶、組織的安全性問題。 然後將所有的發現彙總成一個分數，讓您可以立即得知目前的安全性情況：分數越高，風險層級越低。

安全分數會在 Azure 入口網站頁面中顯示為百分比值，但基礎值也會清楚呈現：

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="以入口網站顯示的整體安全分數":::

若要提高安全性，請參閱「安全性中心」的建議頁面，以瞭解提高分數所需的未完成動作。 每個建議都包含可協助您補救特定問題的指示。

建議會分組為 **安全性控制項**。 每個控制項都是相關安全性建議的邏輯群組，並反映您的易受攻擊面。 只有在您補救控制項內單一資源的「所有」建議後，分數才會改善。 若要瞭解您的組織如何保護每個個別的受攻擊面，請參閱每個安全性控制的分數。

如需詳細資訊，請參閱下方的 [安全分數計算方式](secure-score-security-controls.md#how-your-secure-score-is-calculated) 。 


## <a name="access-your-secure-score"></a>存取您的安全分數

您可以透過 Azure 入口網站或以程式設計方式，找到您的整體安全分數，以及每個訂用帳戶的分數，如下列各節所述：

- [從入口網站取得您的安全分數](#get-your-secure-score-from-the-portal)
- [從 REST API 取得您的安全分數](#get-your-secure-score-from-the-rest-api)
- [從 Azure Resource Graph (ARG 取得您的安全分數) ](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>從入口網站取得您的安全分數

[安全性中心] 會在入口網站中以醒目顯示您的分數：它是 [安全性中心] 總覽頁面的第一個主要磚。 選取此圖格會帶您前往專用的安全分數頁面，您會在其中看到依訂用帳戶細分的分數。 選取單一訂用帳戶，以查看優先建議的詳細清單，以及補救這些建議對訂用帳戶分數的潛在影響。

若要回顧，您的安全分數會顯示在「安全性中心」入口網站頁面中的下列位置。

- 在資訊安全中心 **總覽** (主要儀表板) ：

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="資訊安全中心儀表板上的安全分數":::

- 在 [專用 **安全分數** ] 頁面中：

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="資訊安全中心的安全分數頁面上的安全分數":::

- 在 [ **建議** ] 頁面頂端：

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="資訊安全中心的建議頁面上的安全分數":::

### <a name="get-your-secure-score-from-the-rest-api"></a>從 REST API 取得您的安全分數

您可以透過安全分數 API 來存取您的分數， (目前為預覽) 。 API 方法可讓您靈活地查詢資料，並在一段時間後建立您自己的安全分數報告機制。 例如，您可以使用 [安全分數 API](/rest/api/securitycenter/securescores) 來取得特定訂用帳戶的分數。 此外，您可以使用 [安全分數控制項 API](/rest/api/securitycenter/securescorecontrols) 來列出安全性控制項和您的訂用帳戶的目前分數。

![透過 API 來抓取單一安全分數](media/secure-score-security-controls/single-secure-score-via-api.png)

如需以安全分數 API 為基礎之工具的範例，請參閱 [GitHub 社區的安全分數區域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>從 Azure Resource Graph (ARG 取得您的安全分數) 

Azure Resource Graph 可讓您透過強大的篩選、分組和排序功能，立即存取整個雲端環境中的資源資訊。 這是一種快速且有效率的方式，可透過程式設計方式或從 Azure 入口網站中查詢 Azure 訂用帳戶的資訊。 [深入了解 Azure 資源群組](../governance/resource-graph/index.yml)。

若要使用 ARG 來存取多個訂用帳戶的安全分數：

1. 在 Azure 入口網站中，開啟 **Azure Resource Graph Explorer**。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="啟動 Azure Resource Graph Explorer * * 建議頁面" :::

1. 使用下列範例來輸入您的 Kusto 查詢 (，以取得指引) 。

    - 此查詢會傳回訂用帳戶識別碼、以點為單位的目前分數，以及訂用帳戶的最大分數。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - 此查詢會傳回所有安全性控制項的狀態。 針對每個控制項，您將取得狀況不良的資源數目、目前分數和最大分數。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. 選取 [ **執行查詢**]。




## <a name="tracking-your-secure-score-over-time"></a>追蹤一段時間的安全分數

如果您是具有 Pro 帳戶的 Power BI 使用者，您可以在一段 **時間內使用安全分數** Power BI 儀表板，以追蹤一段時間的安全分數，並調查任何變更。

> [!TIP]
> 您可以在 GitHub 上 Azure 資訊安全中心社區的私人區域中找到此儀表板，以及使用安全分數以程式設計方式運作的其他工具： https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

儀表板包含下列兩份報告，可協助您分析安全性狀態：

- **資源摘要** ：提供資源健康情況的相關摘要資料。
- **安全分數摘要** -提供有關分數進度的摘要資料。 使用「每個訂用帳戶的安全分數」圖表來查看分數中的變更。 如果您注意到分數有顯著的變更，請檢查「偵測到的變更可能會影響您的安全分數」表格，以取得可能造成變更的變更。 此表格顯示已刪除的資源、新部署的資源，或其安全性狀態針對其中一個建議所變更的資源。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="隨時間 PowerBI 儀表板的選擇性安全分數，可追蹤一段時間的安全分數並調查變更":::





## <a name="how-your-secure-score-is-calculated"></a>如何計算您的安全分數 

每個安全性控制項對整體安全分數的貢獻，會清楚地顯示在建議頁面上。

[![增強的安全分數引進了安全性控制項](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

若要獲得安全性控制項的所有可能點數，您所有的資源都必須符合安全性控制項中的所有安全性建議。 例如，資訊安全中心有多個關於如何保護管理連接埠的建議。 您將需要補救它們，以使您的安全分數有所差異。

例如，名為「套用系統更新」的安全性控制項的最高分數為六點，您可以在工具提示中看到控制項的可能增加值：

[![[套用系統更新] 安全性控制項](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

[套用系統更新] 控制項的最高分數 一律為 6。 在此範例中，有 50 個資源。 因此，我們將最高分數除以 50，結果是每個資源佔 0.12 點。 

* **可能增加** (0.12 x 8 個狀況不良的資源 = 0.96) - 控制項中您還能得到的剩餘點數。 如果您補救此控制項中的所有建議，您的分數會增加 2% (在此案例中，0.96 點會進位到 1 點)。 
* **目前分數** (0.12 x 42 個狀況良好的資源 = 5.04) - 此控制項目前的分數。 每個控制項對總分數都有貢獻。 在此範例中，控制項對目前的總安全分數貢獻了 5.04。
* **最高分數** - 完成控制項內所有建議可獲得的點數上限。 控制項的最高分數表示該控制項的相對重要性。 利用最高分數的值，先將問題分級以進行處理。 


### <a name="calculations---understanding-your-score"></a>計算 - 瞭解您的分數

|計量|公式和範例|
|-|-|
|**安全性控制項的目前分數**|<br>![用來計算安全性控制項分數的方程式](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>每個個別安全性控制項都對安全性分數有所貢獻。 受到控制項中建議影響的每個資源，都對控制項的目前分數有所貢獻。 每個控制項的目前分數都是該控制項「內」資源狀態的量值。<br>![顯示計算安全性控制項目前分數時使用之值的工具提示](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此範例中，最高分數 6 會除以 78，因為這是狀況良好和狀況不良的資源數總和。<br>6 / 78 = 0.0769<br>再乘以狀況良好的資源數目 (4)，得到目前的分數：<br>0.0769 * 4 = **0.31**<br><br>|
|**安全分數**<br>單一訂用帳戶|<br>![計算訂用帳戶的安全分數的方程式](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![已啟用所有控制項的單一訂用帳戶安全分數](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>此範例中有一個訂用帳戶，採用所有可用的安全性控制項 (可能的最高分數為 60 點)。 分數顯示出獲得 60 點中的 28 點，其餘的 32 點會反映在安全性控制項的「可能增加的分數」圖中。<br>![控制項清單和可能增加的分數](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全分數**<br>多重訂閱|<br>![用於計算多個訂用帳戶之安全分數的方程式](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>計算多個訂用帳戶的合併分數時，「安全性中心」會針對每個訂用帳戶包含 *權數* 。 您訂用帳戶的相對權數取決於資訊安全中心，例如資源數量等因素。<br>每個訂用帳戶的目前分數計算方式與單一訂用帳戶的計算方式相同，但接著會套用加權，如方程式所示。<br>在檢視多個訂用帳戶時，安全分數會評估所有已啟用原則中的所有資源，並將其對每個安全性控制項的最高分數所造成的綜合影響加以分組。<br>![多個訂用帳戶已啟用所有控制項時的安全分數](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>綜合的分數「不」是平均值，而是所有訂用帳戶中所有資源狀態的評估狀態。<br>在這裡，如果您移至 [建議] 頁面，將可能獲得的點數相加，會發現這就是目前分數 (24) 和可能獲得的最高分數 (60) 之間的差異。|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>哪些建議包含在安全分數計算中？

只有內建建議會影響安全分數。

標示為 **預覽** 的建議不包含在您的安全分數的計算中。 這些建議仍會盡可能進行補救，以在預覽期間結束時計入您的分數。

預覽建議的範例：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="使用預覽旗標的建議":::

## <a name="improve-your-secure-score"></a>改善您的安全分數

若要改善您的安全分數，請補救建議清單中的安全性建議。 您可以針對每個資源手動補救每個建議，或使用 [快速修正!] 選項 (如果有的話)，快速將建議的補救套用至資源群組。 如需詳細資訊，請參閱[補救建議](security-center-remediate-recommendations.md)。

另一種改善分數，並確保您的使用者不會產生對分數造成負面影響的資源，就是設定相關建議的 [強制] 和 [拒絕] 選項。 請在＜[防止強制/拒絕建議的設定錯誤](prevent-misconfigurations.md)＞中深入了解。

## <a name="security-controls-and-their-recommendations"></a>安全性控制項及其建議

下表列出 Azure 資訊安全中心中的安全性控制項。 在每個控制項下，您可以看到當您為「所有」資源補救控制項中列出的「所有」建議時，能為安全分數增加的最高點數。 

針對每個組織環境中的可用資源，各有一組針對安全性中心提供的安全性建議。 您可以藉由 [停用原則](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) ，並 [從建議排除特定資源](exempt-resource.md)，進一步自訂建議。 
 
我們建議每個組織仔細審核其指派的 Azure 原則計畫。 

> [!TIP]
> 如需審核和編輯您的計畫的詳細資訊，請參閱 [使用安全性原則](tutorial-security-policy.md)。 

雖然資訊安全中心的預設安全性計畫是根據業界最佳作法和標準，但在某些情況下，下列內建建議可能無法完全符合您的組織。 因此，有時需要調整預設的計畫，而不會危及安全性，以確保其符合您組織本身的原則。 符合您義務的產業標準、法規標準和基準測試。<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>安全性控制項、分數、說明</b><br></th>
    <th class="tg-cly1"><b>建議</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">啟用 MFA (最高分數 10)</p></strong>如果僅使用密碼來驗證使用者，可能會有不夠嚴密的攻擊媒介。 如果密碼不嚴謹或已於他處公開，那麼以使用者名稱和密碼登入的真的是使用者嗎？<br>啟用 <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> 後，您的帳戶會更安全，而且使用者仍然可以使用單一登入 (SSO) 驗證幾乎所有的應用程式。</td>
    <td class="tg-lboi"; width=55%>- 應為您的訂用帳戶中具有擁有者權限的帳戶啟用 MFA<br>- 應為您的訂用帳戶中具有寫入權限的帳戶啟用 MFA</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">保護管理連接埠 (最高分數 8)</p></strong>暴力密碼破解攻擊的目標是管理連接埠，試圖取得 VM 的存取權。 因為連接埠不一定都需要開啟，所以其中一個風險降低策略就是使用 Just-In-Time 網路存取控制、網路安全性群組、虛擬機器連接埠管理來減少連接埠的暴露程度。<br>由於許多 IT 組織不會封鎖從其網路輸出的 SSH 通訊，攻擊者可以建立加密通道，讓受感染系統上的 RDP 連接埠能夠與攻擊者命令通訊，藉此控制伺服器。 攻擊者可以使用 Windows 遠端管理子系統，在您的環境中橫向移動，並使用偷來的登入資訊存取網路上的其他資源。</td>
    <td class="tg-lboi"; width=55%>- 虛擬機器的管理埠應使用即時網路存取控制來保護<br>-虛擬機器應與網路安全性群組建立關聯<br>-應關閉虛擬機器上的管理連接埠</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">套用系統更新 (最高分數 6)</p></strong>系統更新讓組織能夠維持營運效率、減少安全性弱點，並為使用者提供更穩定的環境。 不套用更新會留下未修補的弱點，造成容易遭受攻擊的環境。 這些弱點可能會遭到入侵，並導致資料遺失、資料外洩、勒索軟體、資源濫用。 若要部署系統更新，您可以使用<a href="/azure/automation/update-management/overview">更新管理解決方案</a>來管理虛擬機器的修補程式和更新。 更新管理就是控制軟體發行的部署和維護。</td>
    <td class="tg-lboi"; width=55%>- 應解決機器上的監視代理程式健康情況問題<br>- 應於虛擬機器擴展集上安裝監視代理程式<br>- 應於機器上安裝監視代理程式<br>- 應更新雲端服務角色的作業系統版本<br>- 應在虛擬機器擴展集上安裝系統更新<br>- 應在機器上安裝系統更新<br>- 應重新啟動機器以套用系統更新<br>- 應將 Kubernetes Service 升級為不易受攻擊的 Kubernetes 版本<br>- 應在虛擬機器上安裝監視代理程式<br>- Log Analytics 代理程式應該安裝在以 Windows 為基礎的 Azure Arc 電腦上 (Preview) <br>- Log Analytics 代理程式應該安裝在以 Linux 為基礎的 Azure Arc 電腦上 (Preview) </td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">補救弱點 (最高分數 6)</p></strong>弱點是威脅執行者可以利用的切入點，藉以危害資源的機密性、可用性或完整性。 <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">管理弱點</a>可減少組織暴露、強化端點介面區、增加組織的復原能力，並減少資源的受攻擊面。 威脅與弱點管理可讓您看到軟體和安全性的錯誤設定，並提供降低風險的建議。</td>
    <td class="tg-lboi"; width=55%>- 應該在 SQL Database 上啟用 Advanced data security<br>- 應補救 Azure Container Registry 映像中的弱點<br>- 應補救 SQL 資料庫的弱點<br>- 弱點評估解決方案應修復弱點<br>- SQL 受控執行個體應啟用弱點評定<br>- SQL 伺服器上應啟用弱點評估<br>- 虛擬機器上應安裝弱點評估解決方案<br>- 容器映射只能從受信任的登錄部署 (預覽版) <br>- 您應在您的叢集上安裝並啟用 Kubernetes Azure 原則附加元件 (preview) </td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">啟用待用加密 (最高分數 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">待用加密</a>可為儲存的資料提供資料保護。 對待用資料的攻擊包括嘗試取得儲存資料之硬體的實體存取權。 Azure 使用對稱式加密來加密和解密大量待用資料。 對稱式加密金鑰可用來在寫入儲存體時將資料加密。 當資料準備好在記憶體中待使用時，該加密金鑰也可用來將資料解密。 金鑰必須儲存在具備以身分識別為基礎的存取控制和稽核原則的安全位置。 其中一個安全位置是 Azure Key Vault。 如果攻擊者取得加密的資料，而不是加密金鑰，攻擊者就無法在不破壞加密的情況下存取資料。</td>
    <td class="tg-lboi"; width=55%>- 應在虛擬機器上套用磁碟加密<br>- 應啟用 SQL Database 上的透明資料加密<br>- 應將自動化帳戶變數加密<br>- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign<br>- SQL 伺服器的 TDE 保護裝置應以您自己的金鑰加密</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">加密傳輸中的資料 (最高分數 4)</p></strong>當資料在元件、位置或程式之間傳輸時，就是「傳輸中」。 無法保護傳輸中資料的組織容易遭受攔截攻擊、竊聽、工作階段攔截。 應使用 SSL/TLS 通訊協定來交換資料，並建議使用 VPN。 透過網際網路在 Azure 虛擬機器和內部部署位置之間傳送加密的資料時，您可以使用虛擬網路閘道 (例如 <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN 閘道</a>) 來傳送加密的流量。</td>
    <td class="tg-lboi"; width=55%>- API 應用程式應只可經由 HTTPS 存取<br>- 函式應用程式應只可經由 HTTPS 存取<br>- 應該只允許對 Azure Cache for Redis 的安全連線<br>- 應啟用儲存體帳戶的安全傳輸<br>- Web 應用程式應只可經由 HTTPS 存取<br>- 于 postgresql 伺服器應啟用私人端點<br>- 應為于 postgresql 資料庫伺服器啟用 [強制執行 SSL 連線]<br>- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]<br>- TLS 應更新為您 API 應用程式的最新版本<br>- TLS 應更新為您函數應用程式的最新版本<br>- TLS 應更新為您 web 應用程式的最新版本<br>- 您的 API 應用程式中應需要 FTPS<br>- 函數應用程式中應需要 FTPS<br>- 您的 web 應用程式中應該需要 FTPS</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">管理存取權與權限 (最高分數 4)</p></strong>安全性計畫的核心部分，是要確保您的使用者擁有執行其作業所需的存取權，但不能超過：<a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">最低權限存取模型</a>。<br>使用 <a href="/azure/role-based-access-control/overview">azure 角色型存取控制 (AZURE RBAC) </a>來建立角色指派，以控制資源的存取權。 角色指派是由三個元素組成：<br>- <strong>安全性主體</strong>：使用者要求存取的物件<br>- <strong>角色定義</strong>：其許可權<br>- <strong>範圍</strong>：套用權限的一組資源</td>
    <td class="tg-lboi"; width=55%>- 應從您的訂用帳戶中移除已淘汰的帳戶 (預覽)<br>- 應從您的訂用帳戶中移除具備擁有者權限的已淘汰帳戶 (預覽)<br>- 應從您的訂用帳戶中移除具備擁有者權限的外部帳戶 (預覽)<br>- 應從您的訂用帳戶中移除具備寫入權限的外部帳戶 (預覽)<br>- 應指派一個以上的擁有者給您的訂用帳戶<br>- Azure 角色型存取控制 (Azure RBAC) 應該用於 Kubernetes Services (Preview) <br>- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證<br>- 應使用服務主體來保護您的訂用帳戶，而不是管理憑證<br>- (預覽的容器應強制執行最低許可權 Linux 功能) <br>- 不可變的 (唯讀) 根目錄檔案系統應針對容器強制執行 (預覽) <br>- 應避免 (預覽版的容器具有許可權擴大) <br>- 以根使用者的形式執行容器應避免 (預覽) <br>- 共用機密主機命名空間的容器應避免 (預覽) <br>- Pod HostPath 磁片區掛接的使用應限制為已知清單 (預覽) <br>- 應避免 (預覽的特殊許可權容器) <br>- 您應在您的叢集上安裝並啟用 Kubernetes Azure 原則附加元件 (preview) <br>- 您的 API 應用程式應使用受控識別<br>- 您的函數應用程式應使用受控識別<br>- 您的 web 應用程式應使用受控識別</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">補救安全性設定 (最高分數 4)</p></strong>設定錯誤的 IT 資產被攻擊的風險較高。 在期限壓力下部署資產時，常會忘記基本的強化動作。 安全性設定錯誤可能發生在基礎結構的任何層級：從作業系統、網路設備到雲端資源。<br>Azure 資訊安全中心會持續比較您的資源設定與業界標準、法規和效能評定的需求。 當您設定好組織重視的相關「合規性套件」(標準和基準) 後，任何差距都會引發安全性建議，包含 CCEID 和潛在安全性影響的說明。<br>常用的套件是 <a href="/azure/security/benchmarks/introduction">Azure 安全性基準測試</a>和 <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure 基礎基準測試版本1.1.0</a>。</td>
    <td class="tg-lboi"; width=55%>- 應補救容器安全性設定中的弱點<br>- 應補救機器上安全性設定的弱點<br>- 應補救虛擬機器擴展集上安全性設定的弱點<br>- 應在虛擬機器上安裝監視代理程式<br>- 應於機器上安裝監視代理程式<br>- Log Analytics 代理程式應該安裝在以 Windows 為基礎的 Azure Arc 電腦上 (Preview) <br>- Log Analytics 代理程式應該安裝在以 Linux 為基礎的 Azure Arc 電腦上 (Preview) <br>- 應於虛擬機器擴展集上安裝監視代理程式<br>- 應解決機器上的監視代理程式健康情況問題<br>- 覆寫或停用容器 AppArmor 設定檔應受限於 (預覽) <br>- 您應在您的叢集上安裝並啟用 Kubernetes Azure 原則附加元件 (preview) </td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">限制未經授權的網路存取 (最高分數 4)</p></strong>組織內的端點可讓您從虛擬網路直接連線到支援的 Azure 服務。 子網路中的所有虛擬機器可以與所有資源通訊。 若要限制與子網內資源之間的通訊，請建立網路安全性群組，並將其與子網建立關聯。 組織可以藉由建立輸入和輸出規則來限制及防範未經授權的流量。</td>
    <td class="tg-lboi"; width=55%>- 應停用虛擬機器上的 IP 轉送<br>- Kubernetes Service 上應定義授權 IP 範圍 (預覽)<br>- (已淘汰) 應限制對 App Service 的存取 (預覽)<br>- (已淘汰) 應強化 IaaS NSG 上 Web 應用程式的規則<br>-虛擬機器應與網路安全性群組建立關聯<br>- CORS 不應允許每項資源存取您的 API 應用程式<br>- CORS 不應允許每項資源存取您的函式應用程式<br>- CORS 不應允許每項資源存取您的 Web 應用程式<br>- 應關閉 API 應用程式的遠端偵錯<br>- 應關閉函式應用程式的遠端偵錯<br>- 應關閉 Web 應用程式的遠端偵錯<br>- 應限制具有網際網路對應 VM 之寬鬆網路安全性群組的存取<br>- 應在網際網路對應的虛擬機器上套用適應性網路強化建議<br>- 您應在您的叢集上安裝並啟用 Kubernetes Azure 原則附加元件 (preview) <br>- 容器應該只在允許的埠上接聽 (預覽) <br>- 服務應該只在允許的埠上接聽 (預覽) <br>- 使用主機網路和埠應受限於 (預覽版) <br>- 虛擬網路應受 Azure 防火牆 (預覽) <br>- 適用于 mariadb 伺服器應啟用私人端點<br>- MySQL 伺服器應啟用私人端點<br>- 于 postgresql 伺服器應啟用私人端點</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">套用自適性應用程式控制 (最高分數 3)</p></strong>自適性應用程式控制 (AAC) 是一種智慧型、自動化的端對端解決方案，可讓您控制哪些應用程式可以在您的 Azure 和非 Azure 機器上執行。 它也有助於強化機器，防範惡意程式碼的攻擊。<br>「安全性中心」會使用機器學習來建立一組機器的已知安全應用程式清單。<br>這套經過核准的應用程式清單可提供安全性優點，而不需要管理複雜性。<br>AAC 特別適合需要執行一組特定應用程式的特殊用途伺服器。</td>
    <td class="tg-lboi"; width=55%>- 應在虛擬機器上啟用自適性應用程式控制<br>- 應在虛擬機器上安裝監視代理程式<br>- 應於機器上安裝監視代理程式<br>- Log Analytics 代理程式應該安裝在以 Windows 為基礎的 Azure Arc 電腦上 (Preview) <br>- Log Analytics 代理程式應該安裝在以 Linux 為基礎的 Azure Arc 電腦上 (Preview) <br>- 應解決機器上的監視代理程式健康情況問題</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">套用資料分類 (最高分數 2)</p></strong>依據敏感度和業務衝擊為組織的資料分類，可讓您判斷和指派資料的值，並提供管理的策略和基礎。<br><a href="/azure/information-protection/what-is-information-protection">Azure 資訊保護</a>可以協助資料分類。 它會使用加密、身分識別、授權原則來保護資料，並限制資料存取。 Microsoft 使用的其中幾個分類是非商務、公用、一般、機密、高度機密。</td>
    <td class="tg-lboi"; width=55%>- SQL 資料庫中的敏感性資料應分類 (預覽)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">保護應用程式防範 DDoS 攻擊 (最高分數 2)</p></strong>分散式阻斷服務 (DDoS) 攻擊會使資源無法招架，導致應用程式無法使用。 使用 <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS 保護標準</a>來保護您的組織，防範三種主要的 DDoS 攻擊：<br>- <strong>巨流量攻擊</strong>以合法的流量淹沒網路。 「DDoS 保護標準」會自動吸收或清除這些攻擊，以減輕這些攻擊。<br>- <strong>通訊協定攻擊</strong>透過利用第 3 層和第 4 層通訊協定堆疊中的弱點，讓目標無法供存取。 「DDoS 保護標準」會封鎖惡意流量，以減輕這些攻擊。<br>- <strong>資源 (應用程式) 層攻擊</strong>以 Web 應用程式封包為目標。 使用 Web 應用程式防火牆和 DDoS 保護標準來防禦此類型攻擊。</td>
    <td class="tg-lboi"; width=55%>- 應啟用 Azure DDoS 保護標準<br>- 應 (預覽版來強制執行容器 CPU 和記憶體限制) <br>- 您應在您的叢集上安裝並啟用 Kubernetes Azure 原則附加元件 (preview) </td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">啟用端點保護 (最高分數 2)</p></strong>為了確保您的端點能防禦惡意程式碼，行為感應器會從端點的作業系統收集資料並加以處理，然後將資料傳送至私人雲端進行分析。 安全性分析會運用大數據、機器學習和其他來源，建議對威脅的回應。 例如，<a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> 會使用威脅情報來識別攻擊方法並產生安全性警示。<br>資訊安全中心支援下列端點保護問題：Windows Defender、System Center Endpoint Protection、Trend Micro、Symantec v12.1.1.1100、McAfee v10 for Windows、McAfee v10 for Linux、Sophos v9 for Linux。 如果資訊安全中心偵測到上述任何解決方案，就不會再顯示安裝端點保護的建議。</td>
    <td class="tg-lboi"; width=55%>- 應補救虛擬機器擴展集上失敗的端點保護健康情況<br>- 應解決機器上端點保護健康情況的問題<br>- 應在虛擬機器擴展集上安裝端點保護解決方案<br>- 在虛擬機器上安裝端點保護解決方案<br>- 應解決機器上的監視代理程式健康情況問題<br>- 應於虛擬機器擴展集上安裝監視代理程式<br>- 應於機器上安裝監視代理程式<br>- 應在虛擬機器上安裝監視代理程式<br>- Log Analytics 代理程式應該安裝在以 Windows 為基礎的 Azure Arc 電腦上 (Preview) <br>- Log Analytics 代理程式應該安裝在以 Linux 為基礎的 Azure Arc 電腦上 (Preview) <br>- 在您的機器上安裝端點保護解決方案</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">啟用稽核和記錄功能 (最高分數 1)</p></strong>記錄資料可讓您深入解析過去的問題、防止可能的問題、改善應用程式效能，並提供將手動動作自動化的功能。<br>- <strong>控制和管理記錄</strong>提供 <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> 作業的相關資訊。<br>- <strong>資料平面記錄</strong>提供使用 Azure 資源時所引發事件的相關資訊。<br>- <strong>已處理的事件</strong>提供分析已處理事件/警示的相關資訊。</td>
    <td class="tg-lboi"; width=55%>- 應啟用 SQL 伺服器上的稽核<br>- 應啟用 App Service 中的診斷記錄<br>- 應啟用 Azure Data Lake Store 中的診斷記錄<br>- 應啟用 Azure 串流分析中的診斷記錄<br>- 應啟用 Batch 帳戶中的診斷記錄<br>- 應啟用 Data Lake Analytics 中的診斷記錄<br>- 應啟用事件中樞中的診斷記錄<br>- 應啟用 IoT 中樞中的診斷記錄<br>- 應啟用 Key Vault 中的診斷記錄<br>- 應啟用 Logic Apps 中的診斷記錄<br>- 應啟用搜尋服務中的診斷記錄<br>- 應啟用服務匯流排中的診斷記錄<br>- 應啟用虛擬機器擴展集中的診斷記錄<br>- Batch 帳戶上應設定計量警示規則<br>- SQL 稽核設定應已設定動作群組來擷取重要活動<br>- SQL 伺服器應設定有 90 天以上的稽核保留天數</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">啟用 advanced 威脅防護 (最大分數 0) </p></strong>Azure 資訊安全中心的選用 Azure Defender 威脅防護方案可為您的環境提供完整的防禦。 資訊安全中心在您環境的任何區域中偵測到威脅時，會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。<br>每個 Azure Defender 方案都是個別的選擇性供應專案，您可以使用此安全性控制中的相關建議來啟用此功能。<br><a href="/azure/security-center/threat-protection">深入瞭解資訊安全中心的威脅防護</a>。</td>
    <td class="tg-lboi"; width=55%>- Azure SQL Database 伺服器上應啟用 Advanced data security<br>- 應在電腦上的 SQL server 上啟用 Advanced data security<br>- 應在虛擬機器上啟用 Advanced 威脅防護<br>- Azure App Service 方案上應啟用 Advanced 威脅防護<br>- 應在 Azure 儲存體帳戶上啟用 Advanced 威脅防護<br>- 應在 Azure Kubernetes Service 叢集上啟用 Advanced 威脅防護<br>- 應在 Azure Container Registry 登錄上啟用 Advanced 威脅防護<br>- 應在 Azure Key Vault 保存庫上啟用 Advanced 威脅防護</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">實作安全性最佳做法 (最高分數 0)</p></strong>現代的安全性最佳做法「假定」網路周邊有缺口。 基於這個理由，此控制項中的許多最佳做法都著重於管理身分識別。<br>遺失金鑰和認證是相當常見的問題。 <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> 藉由加密金鑰、.pfx 檔案、密碼來保護金鑰和秘密。<br>虛擬私人網路 (VPN) 是存取虛擬機器的安全方式。 如果 Vpn 無法使用，請使用複雜的複雜密碼和雙因素驗證，例如 <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD Multi-Factor Authentication</a>。 雙重要素驗證可避免只依賴使用者名稱和密碼而固有的弱點。<br>使用增強式驗證和授權平台是另一個最佳做法。 使用同盟身分識別可讓組織將已授權身分的管理委派他人。 當員工離職，而必須撤銷他們的存取權時，這很重要。</td>
    <td class="tg-lboi"; width=55%>- 應為您的訂用帳戶指定最多 3 位擁有者<br>- 應從您的訂用帳戶中移除具備讀取權限的外部帳戶<br>- 應為您的訂用帳戶中具有讀取權限的帳戶啟用 MFA<br>- 應限制存取具防火牆與虛擬網路設定的儲存體帳戶<br>- 除了 RootManageSharedAccessKey 外，應從事件中樞命名空間移除所有授權規則<br>- 應針為 SQL 伺服器佈建 Azure Active Directory 管理員<br>- 應啟用受控執行個體的進階資料安全性<br>- 應定義事件中樞執行個體的授權規則<br>- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源<br>- 虛擬機器應移轉至新的 Azure Resource Manager 資源<br>- 子網路應該與網路安全性群組建立關聯<br>- [預覽] 應啟用 Windows 惡意探索防護 <br>- [預覽] 應安裝來賓設定代理程式<br>- 應使用網路安全性群組保護非網際網路面向的虛擬機器<br>- 應針對虛擬機器啟用 Azure 備份<br>- 應針對適用於 MariaDB 的 Azure 資料庫啟用地理位置冗余備份<br>- 應針對適用於 MySQL 的 Azure 資料庫啟用地理位置冗余備份<br>- 應針對適用於 PostgreSQL 的 Azure 資料庫啟用地理位置冗余備份<br>- PHP 應更新為您的 API 應用程式的最新版本<br>- PHP 應更新為您 web 應用程式的最新版本<br>- 您的 API 應用程式應更新為最新版本的 JAVA<br>- JAVA 應更新為您函數應用程式的最新版本<br>- JAVA 應更新為您 web 應用程式的最新版本<br>- Python 應更新為您的 API 應用程式的最新版本<br>- Python 應更新為您函數應用程式的最新版本<br>- Python 應更新為您 web 應用程式的最新版本<br>- SQL server 的審核保留期應設定為至少90天<br>- Web apps 應要求所有連入要求的 SSL 憑證</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>安全分數常見問題集

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果我只解決安全性控制項四個建議中的三個，我的安全分數是否會改變？
否。 在您補救單一資源的所有建議之前，分數不會變更。 若要取得控制項的最高分數，您必須補救所有資源的所有建議。

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果建議不適用於我，而我在原則中停用建議，我的安全性控制項是否會完成，而且我的安全分數也會更新？
是。 建議您在建議不適合您的環境時予以停用。 如需有關如何停用特定建議的指示，請參閱[停用安全性原則](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果安全性控制項給我的安全分數是零點，我應該忽略它嗎？
在某些情況下，您會看到控制項的最高分數大於零，但影響是零。 當修正資源的分數增量可以忽略時，就會四捨五入為零。 請勿忽略這些建議，因為它們仍然會帶來安全性的改善。 唯一的例外是「其他最佳做法」控制項。 補救這些建議並不會增加您的分數，但會增強您的整體安全性。

## <a name="next-steps"></a>後續步驟

本文說明安全分數和其引進的安全性控制項。 如需相關內容，請參閱下列文章：

- [瞭解建議的不同元素](security-center-recommendations.md)
- [瞭解如何補救建議](security-center-remediate-recommendations.md)
- [觀看以程式設計方式使用安全分數的 GitHub 型工具](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)