---
title: 使用 Azure Private Link 安全地將網路連線到 Azure 自動化
description: 使用 Azure Private Link 安全地將網路連線到 Azure 自動化
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: a4985784a17f2e0350a7b2c7a4f62f574862d50c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714348"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>使用 Azure Private Link 可將網路安全地連線至 Azure 自動化 (preview) 

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將自動化服務帶入您的 VNet 中。 VNet 上的機器和自動化帳戶之間的網路流量會透過 VNet 和 Microsoft 骨幹網路上的私人連結來進行，而不會從公用網際網路暴露。

例如，您有已停用輸出網際網路存取的 VNet。 不過，您想要私下存取自動化帳戶，並在混合式 Runbook 背景工作角色上使用 Webhook、狀態設定和 runbook 作業等自動化功能。 此外，您也想要讓使用者只能透過 VNET 存取自動化帳戶。  部署私人端點可達成這些目標。

本文涵蓋使用方式，以及如何使用自動化帳戶設定私人端點 (預覽) 。

![Azure 自動化的 Private Link 概念總覽](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> 只有 Azure 商業和 Azure 美國政府雲端才提供 Azure 自動化 (preview) 的 Private Link 支援。

## <a name="advantages"></a>優點

有了私人連結，您可以：

- 私下連接到 Azure 自動化，而不需要開啟任何公用網路存取權。
- 私下連線至 Azure 監視器 Log Analytics 工作區，而不需開啟任何公用網路存取權。

    >[!NOTE]
    >如果您的自動化帳戶連結至 Log Analytics 工作區來轉送作業資料，以及啟用了如更新管理、變更追蹤和清查、狀態設定或停機期間啟動/停止 VM 等功能，則這是必要的。 如需 Azure 監視器 Private Link 的詳細資訊，請參閱 [使用 Azure Private Link 安全地將網路連接到 Azure 監視器](../../azure-monitor/platform/private-link-security.md)。

- 確定您的自動化資料只能透過授權的私人網路進行存取。
- 藉由定義可透過私人端點連接的 Azure 自動化資源，以防止從您的私人網路遭到外泄資料。
- 使用 ExpressRoute 和 Private Link，安全地將私人內部部署網路連線到 Azure 自動化。
- 保留 Microsoft Azure 骨幹網路內的所有流量。

如需詳細資訊，請參閱[私人連結的主要優點](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>運作方式

Azure 自動化 Private Link 會將一個或多個私人端點連線 (，因此它們所包含的虛擬網路) 至您的自動化帳戶資源。 這些端點是使用 webhook 來啟動 runbook 的機器、裝載混合式 Runbook 背景工作角色的機器，以及 DSC 節點。

在您建立自動化的私人端點之後，您或電腦可以直接聯繫的每個公開的自動化 Url 都會對應到您 VNet 中的一個私人端點。

作為預覽版本的一部分，自動化帳戶無法存取使用私人端點保護的 Azure 資源。 例如，Azure Key Vault、Azure SQL、Azure 儲存體帳戶等等。

### <a name="webhook-scenario"></a>Webhook 案例

您可以在 webhook URL 上執行 POST 來啟動 runbook。 例如，URL 看起來像這樣： `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>狀態設定 (>.agentsvc.azure-automation.net) 案例

狀態設定提供 Azure 設定管理服務，可讓您針對任何雲端或內部部署資料中心內的節點，撰寫、管理及編譯 PowerShell Desired State Configuration (DSC) 設定。

電腦上的代理程式會向 DSC 服務註冊，然後使用服務端點來提取 DSC 設定。 Agent 服務端點看起來像這樣： `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` 。

公用 & 私人端點的 URL 相同，不過，在啟用 Private link 時，它會對應到私人 IP 位址。

## <a name="planning-based-on-your-network"></a>根據您的網路進行規劃

在設定您的自動化帳戶資源之前，請先考慮您的網路隔離需求。 評估您的虛擬網路對公用網際網路的存取權，以及您自動化帳戶的存取限制 (包括將 Private Link 群組範圍設定為 Azure 監視器的記錄（如果與您的自動化帳戶) 整合）。 此外，也請在您的計畫中包含自動化服務 [DNS 記錄](./automation-region-dns-records.md) 的評論，以確保支援的功能能正常運作，而不會發生問題。

### <a name="connect-to-a-private-endpoint"></a>連線到私人端點

建立私人端點以連接我們的網路。 您可以在 [Azure 入口網站 Private Link center](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)中建立它。 套用您對將 publicnetworkaccess 新增和 private link 的變更之後，最多可能需要35分鐘的時間才會生效。

在本節中，您將為您的自動化帳戶建立私人端點。

1. 在畫面的左上方，選取 [ **建立資源 > 網路 > Private Link 中心 (預覽]) **。

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
    |私人 DNS 區域 |選取 * (新增) privatelink.azure-automation.net* |
    |||

8. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

9. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

在 **Private Link Center (Preview) **中，選取 **私人端點** 以查看您的私人連結資源。

![Automation 資源私用連結](./media/private-link-security/private-link-automation-resource.png)

選取資源以查看所有詳細資料。 這會為您的自動化帳戶建立新的私人端點，並從您的虛擬網路將私人 IP 指派給它。 **連接狀態**會顯示為 [**已核准**]。

同樣地，狀態設定 (>.agentsvc.azure-automation.net) 和混合式 Runbook 背景工作的工作執行時間 (jrds) 中，會建立唯一的完整功能變數名稱 (FQDN) 。 每個系統會從您的 VNet 指派不同的 IP，而 **線上狀態** 會顯示為 [ **已核准**]。

如果服務取用者具有自動化資源的 Azure RBAC 許可權，就可以選擇自動核准方法。 在此情況下，當要求到達 Automation 提供者資源時，服務提供者不需要採取任何動作，而且會自動核准連接。

## <a name="set-public-network-access-flags"></a>設定公用網路存取旗標

您可以設定自動化帳戶以拒絕所有的公用設定，並只允許透過私人端點的連線，以進一步強化網路安全性。 如果您想要限制只能從 VNet 記憶體取自動化帳戶，且不允許來自公用網際網路的存取，您可以將 `publicNetworkAccess` 屬性設定為 `$false` 。

當 **公用網路存取** 設定為時 `$false` ，只允許透過私人端點的連線，而且會拒絕透過公用端點的所有連線，並出現 unathorized 錯誤訊息和 HTTP 狀態401。 

下列 PowerShell 腳本會示範如何 `Get` 與 `Set` 自動化帳戶層級的 **公用網路存取** 屬性：

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>DNS 組態

使用完整網域名稱 (FQDN) 作為連接字串的一部分來連線到私人連結資源時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 您應檢查並更新您的 DNS 設定，以使用您的私人端點進行連接。

與私人端點相關聯的網路介面包含設定 DNS 時所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 與私人 IP 位址。

您可以使用下列選項來設定私人端點的 DNS 設定：

* 使用主機檔案 (只建議用於測試) 。 您可以使用虛擬機器上的主機檔案，先覆寫使用 DNS 進行名稱解析。

* 使用 [私人 DNS 區域](../../dns/private-dns-privatednszone.md)。 您可以使用私人 DNS 區域覆寫特定私人端點的 DNS 解析。 私人 DNS 區域可以連結至您的虛擬網路，以解析特定網域。 若要讓虛擬機器上的代理程式能透過私人端點進行通訊，請將私人 DNS 記錄建立為 `privatelink.azure-automation.net` 。 將新的 DNS *a* 記錄對應新增至私人端點的 IP。

* 使用您的 DNS 轉寄站 (選擇性) 。 您可以使用 DNS 轉寄站來覆寫特定私人連結資源的 DNS 解析。 如果您的 [DNS 伺服器](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)裝載在虛擬網路上，可以建立 DNS 轉送規則來使用私人 DNS 區域，以簡化所有私人連結資源的設定。

如需詳細資訊，請參閱 [Azure 私人端點 DNS](../../private-link/private-endpoint-dns.md)設定。

## <a name="next-steps"></a>後續步驟

若要深入瞭解私人端點，請參閱 [什麼是 Azure 私人端點？](../../private-link/private-endpoint-overview.md)。