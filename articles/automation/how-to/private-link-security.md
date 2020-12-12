---
title: 使用 Azure Private Link 安全地將網路連線到 Azure 自動化
description: 使用 Azure Private Link 安全地將網路連線到 Azure 自動化
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.openlocfilehash: 26e7dbf3f5629d4691211b6c9b82446ba4035421
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347608"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>使用 Azure Private Link 安全地將網路連線到 Azure 自動化

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將自動化服務帶入您的 VNet 中。 VNet 上的機器和自動化帳戶之間的網路流量會透過 VNet 和 Microsoft 骨幹網路上的私人連結來進行，而不會從公用網際網路暴露。

例如，您有已停用輸出網際網路存取的 VNet。 不過，您想要私下存取自動化帳戶，並在混合式 Runbook 背景工作角色上使用 Webhook、狀態設定和 runbook 作業等自動化功能。 此外，您也想要讓使用者只能透過 VNET 存取自動化帳戶。  部署私人端點可達成這些目標。

本文涵蓋使用時機，以及如何使用您的自動化帳戶設定私人端點。

![Azure 自動化的 Private Link 概念總覽](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> 只有在 Azure 商業和 Azure 美國政府雲端中，才能使用 Azure 自動化的 Private Link 支援。

## <a name="advantages"></a>優點

有了私人連結，您可以：

- 私下連接到 Azure 自動化，而不需要開啟任何公用網路存取權。
- 私下連線至 Azure 監視器 Log Analytics 工作區，而不需開啟任何公用網路存取權。

    >[!NOTE]
    >如果您的自動化帳戶連結至 Log Analytics 工作區來轉送作業資料，而且您已啟用諸如更新管理、變更追蹤和清查、狀態設定或停機期間啟動/停止 VM 等功能，則需要 Log Analytics 工作區的個別私人端點。 如需 Azure 監視器 Private Link 的詳細資訊，請參閱 [使用 Azure Private Link 安全地將網路連接到 Azure 監視器](../../azure-monitor/platform/private-link-security.md)。

- 確定您的自動化資料只能透過授權的私人網路進行存取。
- 藉由定義可透過私人端點連接的 Azure 自動化資源，以防止從您的私人網路遭到外泄資料。
- 使用 ExpressRoute 和 Private Link，安全地將私人內部部署網路連線到 Azure 自動化。
- 保留 Microsoft Azure 骨幹網路內的所有流量。

如需詳細資訊，請參閱[私人連結的主要優點](../../private-link/private-link-overview.md#key-benefits)。

## <a name="limitations"></a>限制

- 在目前的 Private Link 的執行中，自動化帳戶雲端作業無法存取使用私人端點保護的 Azure 資源。 例如，Azure Key Vault、Azure SQL、Azure 儲存體帳戶等等。若要解決此問題，請改用 [混合式 Runbook 背景工作角色](../automation-hybrid-runbook-worker.md) 。
- 您必須使用最新版的 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md) 來進行 Windows 或 Linux。
- [Log Analytics 閘道](../../azure-monitor/platform/gateway.md)不支援 Private Link。

## <a name="how-it-works"></a>運作方式

Azure 自動化 Private Link 會將一個或多個私人端點連線 (，因此它們所包含的虛擬網路) 至您的自動化帳戶資源。 這些端點是使用 webhook 來啟動 runbook 的機器、裝載混合式 Runbook 背景工作角色的電腦，以及 Desired State Configuration (DSC) 節點。

在您建立自動化的私人端點之後，每個公開的自動化 Url 都會對應到您 VNet 中的一個私人端點。 您或電腦可以直接聯繫自動化 Url。

### <a name="webhook-scenario"></a>Webhook 案例

您可以在 webhook URL 上執行 POST 來啟動 runbook。 例如，URL 看起來像這樣： `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>混合式 Runbook 背景工作案例

Azure 自動化的使用者混合式 Runbook 背景工作角色功能可讓您直接在 Azure 或非 Azure 電腦上執行 runbook，包括已在啟用 Azure Arc 的伺服器上註冊的伺服器。 從裝載角色的電腦或伺服器上，您可以直接在其上執行 runbook，並針對環境中的資源執行 runbook，以管理這些本機資源。

混合式背景工作角色會使用 JRDS 端點來啟動/停止 runbook、將 runbook 下載至背景工作，以及將作業記錄資料流程傳回至自動化服務。啟用 JRDS 端點之後，URL 看起來會像這樣： `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` 。 這可確保連線到 Azure 虛擬網路的混合式背景工作角色上執行的 runbook 能夠執行工作，而不需要開啟連至網際網路的輸出連線。  

> [!NOTE]
>使用 Azure 自動化的最新私用連結時，它只支援在連線到 Azure 虛擬網路的混合式 Runbook 背景工作角色上執行作業，而且不支援雲端作業。

## <a name="hybrid-worker-scenario-for-update-management"></a>更新管理的混合式背景工作角色案例  

系統混合式 Runbook 背景工作角色支援一組隱藏的 runbook，這些 runbook 是設計用來在 Windows 和 Linux 電腦上安裝使用者指定更新的更新管理功能。 當 Azure 自動化更新管理啟用時，任何連線到 Log Analytics 工作區的電腦都會自動設定為系統混合式 Runbook 背景工作角色。

若要瞭解 & 設定 [更新管理](../update-management/overview.md)的更新管理評論。 更新管理功能相依于 Log Analytics 工作區，因此需要將工作區連結至自動化帳戶。 Log Analytics 工作區會儲存解決方案所收集的資料，並裝載其記錄搜尋和流覽。

如果您想要將電腦設定為讓更新管理以安全的 Private Link 方式連線到自動化 & Log Analytics 工作區，您必須啟用與以 Private Link 設定的自動化帳戶連結的 Log Analytics 工作區 Private Link。

您可以遵循 [設定 Log analytics](../../azure-monitor/platform/private-link-security.md#configure-log-analytics)中所述的步驟，來控制如何從 Private Link 範圍外部連線到 Log Analytics 工作區。 如果您將 [允許擷取的公用網路存取] 設為 [否]，則連線範圍以外的電腦就無法將資料上傳到此工作區。 如果您將 [允許查詢的公用網路存取] 設為 [否]，則範圍以外的電腦就無法存取此工作區中的資料。

使用 **DSCAndHybridWorker** 目標子資源來啟用使用者 & 系統混合式背景工作角色的 Private Link。

> [!NOTE]
> 裝載于 Azure 外部的機器是由更新管理管理，並透過 ExpressRoute 私用對等互連、VPN 通道和使用私人端點的對等互連虛擬網路連線到 Azure VNet，以支援 Private Link。

### <a name="state-configuration-agentsvc-scenario"></a>狀態設定 (>.agentsvc.azure-automation.net) 案例

狀態設定提供 Azure 設定管理服務，可讓您針對任何雲端或內部部署資料中心內的節點，撰寫、管理及編譯 PowerShell Desired State Configuration (DSC) 設定。

電腦上的代理程式會向 DSC 服務註冊，然後使用服務端點來提取 DSC 設定。 Agent 服務端點看起來像這樣： `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` 。

公用 & 私人端點的 URL 相同，不過，在啟用 Private link 時，它會對應到私人 IP 位址。

## <a name="planning-based-on-your-network"></a>根據您的網路進行規劃

在設定您的自動化帳戶資源之前，請先考慮您的網路隔離需求。 評估您的虛擬網路對公用網際網路的存取權，以及您自動化帳戶的存取限制 (包括將 Private Link 群組範圍設定為 Azure 監視器的記錄（如果與您的自動化帳戶) 整合）。 此外，也請在您的計畫中包含自動化服務 [DNS 記錄](./automation-region-dns-records.md) 的評論，以確保支援的功能能正常運作，而不會發生問題。

### <a name="connect-to-a-private-endpoint"></a>連線到私人端點

建立私人端點以連接我們的網路。 您可以在 [Azure 入口網站 Private Link center](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)中建立它。 套用您對將 publicnetworkaccess 新增和 Private Link 所做的變更之後，最多可能需要35分鐘的時間才會生效。

在本節中，您將為您的自動化帳戶建立私人端點。

1. 在畫面的左上方，選取 [ **建立資源 > 網路] > Private Link 中心**。

2. 在 [私人連結中心 - 概觀] 中，選取 [與服務建立私人連線] 選項上的 [開始]。

3. 在 [ **建立虛擬機器-基本**] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入您的 *PrivateEndpoint*。 |
    | 區域 | 選取 [ **YourRegion**]。 |
    |||

4. 完成時，選取 [下一步:資源]。

5. 在 [ **建立私人端點-資源**] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 [連線到我目錄中的 Azure 資源]。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [ **Microsoft. Automation/automationAccounts**]。 |
    | 資源 |選取 *myAutomationAccount*|
    |目標子資源 |視您的案例而定，選取 *Webhook* 或 *DSCAndHybridWorker* 。|
    |||

6. 完成時，選取 [下一步:組態]。

7. 在 [ **建立私人端點-** 設定] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路**| |
    | 虛擬網路| 選取 [MyVirtualNetwork]。 |
    | 子網路 | 選取 [mySubnet]。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]。 |
    |私人 DNS 區域 |選取 *(新增) privatelink.azure-automation.net* |
    |||

8. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

9. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

在 **Private Link 中心** 內，選取 **私人端點** 以查看您的私人連結資源。

![Automation 資源私用連結](./media/private-link-security/private-link-automation-resource.png)

選取資源以查看所有詳細資料。 這會為您的自動化帳戶建立新的私人端點，並從您的虛擬網路將私人 IP 指派給它。 **連接狀態** 會顯示為 [**已核准**]。

同樣地，狀態設定 (>.agentsvc.azure-automation.net) 和混合式 Runbook 背景工作的工作執行時間 (jrds) 中，會建立唯一的完整功能變數名稱 (FQDN) 。 每個系統會從您的 VNet 指派不同的 IP，而 **線上狀態** 會顯示為 [ **已核准**]。

如果服務取用者具有自動化資源的 Azure RBAC 許可權，就可以選擇自動核准方法。 在此情況下，當要求到達 Automation 提供者資源時，服務提供者不需要採取任何動作，而且會自動核准連接。

## <a name="set-public-network-access-flags"></a>設定公用網路存取旗標

您可以設定自動化帳戶以拒絕所有的公用設定，並只允許透過私人端點的連線，以進一步強化網路安全性。 如果您想要限制只能從 VNet 記憶體取自動化帳戶，且不允許來自公用網際網路的存取，您可以將 `publicNetworkAccess` 屬性設定為 `$false` 。

當 **公用網路存取** 設定為時 `$false` ，只允許透過私人端點的連線，而透過公用端點的所有連線都會遭到拒絕，並出現未經授權的錯誤訊息和 HTTP 狀態401。

下列 PowerShell 腳本會示範如何 `Get` 與 `Set` 自動化帳戶層級的 **公用網路存取** 屬性：

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

您也可以從 Azure 入口網站控制公用網路存取屬性。 從您的自動化帳戶中，從左側窗格中選取 [**帳戶設定**] 區段底下的 [**網路隔離**]。 當公用網路存取設定為 [ **否**] 時，只允許透過私人端點的連線，而且會拒絕透過公用端點的所有連線。

![公用網路存取設定](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS 組態

使用完整網域名稱 (FQDN) 作為連接字串的一部分來連線到私人連結資源時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 您應檢查並更新您的 DNS 設定，以使用您的私人端點進行連接。

與私人端點相關聯的網路介面包含設定 DNS 時所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 與私人 IP 位址。

您可以使用下列選項來設定私人端點的 DNS 設定：

* 使用主機檔案 (只建議用於測試) 。 您可以使用虛擬機器上的主機檔案，先覆寫使用 DNS 進行名稱解析。 您的 DNS 專案看起來應該如下列範例所示： `privatelinkFQDN.jrds.sea.azure-automation.net` 。

* 使用 [私人 DNS 區域](../../dns/private-dns-privatednszone.md)。 您可以使用私人 DNS 區域覆寫特定私人端點的 DNS 解析。 私人 DNS 區域可以連結至您的虛擬網路，以解析特定網域。 若要讓虛擬機器上的代理程式能透過私人端點進行通訊，請將私人 DNS 記錄建立為 `privatelink.azure-automation.net` 。 將新的 DNS *a* 記錄對應新增至私人端點的 IP。

* 使用您的 DNS 轉寄站 (選擇性) 。 您可以使用 DNS 轉寄站來覆寫特定私人連結資源的 DNS 解析。 如果您的 [DNS 伺服器](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)裝載在虛擬網路上，可以建立 DNS 轉送規則來使用私人 DNS 區域，以簡化所有私人連結資源的設定。

如需詳細資訊，請參閱 [Azure 私人端點 DNS](../../private-link/private-endpoint-dns.md)設定。

## <a name="next-steps"></a>後續步驟

若要深入瞭解私人端點，請參閱 [什麼是 Azure 私人端點？](../../private-link/private-endpoint-overview.md)。