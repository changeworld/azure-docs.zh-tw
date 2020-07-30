---
title: 使用 Azure 私人連結將網路安全地連接到 Azure 自動化
description: 使用 Azure 私人連結將網路安全地連接到 Azure 自動化
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: c81d9774dccf8c02d2eab7b1ebbb69e6671869e8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423791"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>使用 Azure 私人連結將網路安全地連接到 Azure 自動化（預覽）

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將自動化服務帶入您的 VNet。 VNet 上的機器與自動化帳戶之間的網路流量會流經 VNet，並在 Microsoft 骨幹網路上進行私人連結，以消除公開網際網路的風險。

例如，您有已停用輸出網際網路存取的 VNet。 不過，您想要私下存取您的自動化帳戶，並在混合式 Runbook 背景工作角色上使用 Webhook、狀態設定和 runbook 作業等自動化功能。 此外，您想要讓使用者只能透過 VNET 存取自動化帳戶。  部署私用端點可達到這些目標。

本文涵蓋時機，以及如何使用您的自動化帳戶（預覽）來設定私用端點。

![Azure 自動化的私用連結概念總覽](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Azure 自動化（預覽）的私人連結支援僅適用于 Azure 商業和 Azure 美國政府雲端。

## <a name="advantages"></a>優點

有了私人連結，您可以：

- 私下連接到 Azure 自動化，而不需要開啟任何公用網路存取。
- 私下連接到 Azure 監視器 Log Analytics 工作區，而不需開啟任何公用網路存取。

    >[!NOTE]
    >如果您的自動化帳戶連結至 Log Analytics 工作區以轉送作業資料，而且您已啟用如更新管理、變更追蹤和清查、狀態設定，或在離峰期間啟動/停止 Vm 等功能，則這是必要的。 如需 Azure 監視器私人連結的詳細資訊，請參閱[使用 Azure 私人連結將網路安全地連接到 Azure 監視器](../../azure-monitor/platform/private-link-security.md)。

- 確保您的自動化資料只能透過授權的私人網路存取。
- 藉由定義透過私人端點連接的 Azure 自動化資源，防止從您的私人網路外泄資料。
- 使用 ExpressRoute 和私人連結，將私人內部部署網路安全地連接到 Azure 自動化。
- 保留 Microsoft Azure 骨幹網路內的所有流量。

如需詳細資訊，請參閱[私人連結的主要優點](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>運作方式

Azure 自動化私人連結會將一或多個私人端點（也就是它們所包含的虛擬網路）連接到您的自動化帳戶資源。 這些端點是使用 webhook 來啟動 runbook 的電腦、裝載混合式 Runbook 背景工作角色的機器，以及 DSC 節點。

建立自動化的私用端點之後，您或電腦可以直接聯繫的每個公開的自動化 Url 都會對應至 VNet 中的一個私人端點。

作為預覽版本的一部分，自動化帳戶無法存取使用私用端點保護的 Azure 資源。 例如，Azure Key Vault、Azure SQL、Azure 儲存體帳戶等。

### <a name="webhook-scenario"></a>Webhook 案例

您可以在 webhook URL 上執行 POST 來啟動 runbook。 例如，URL 看起來像這樣：`https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>狀態設定（agentsvc）案例

狀態設定提供 Azure 設定管理服務，可讓您針對任何雲端或內部部署資料中心的節點，撰寫、管理和編譯 PowerShell Desired State Configuration （DSC）設定。

電腦上的代理程式會向 DSC 服務註冊，然後使用服務端點來提取 DSC 設定。 Agent 服務端點看起來像這樣： `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` 。

公用 & 私人端點的 URL 會相同，但在啟用私人連結時，它會對應至私人 IP 位址。

## <a name="planning-based-on-your-network"></a>根據您的網路進行規劃

在設定您的自動化帳戶資源之前，請考慮您的網路隔離需求。 評估您的虛擬網路對公用網際網路的存取權，以及自動化帳戶的存取限制（包括將私人連結群組範圍設定為 Azure 監視器記錄（如果與您的自動化帳戶整合）。 也請在您的計畫中納入自動化服務[DNS 記錄](./automation-region-dns-records.md)的審查，以確保支援的功能不會有任何問題。

### <a name="connect-to-a-private-endpoint"></a>連線到私人端點

建立私人端點來連接我們的網路。 您可以在 [ [Azure 入口網站私人連結中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)] 中建立它。 套用 publicNetworkAccess 和私用連結的變更後，最多可能需要35分鐘的時間，才會生效。

在本節中，您將為自動化帳戶建立私人端點。

1. 在畫面的左上方，選取 [**建立資源] > [網路 > 私人連結中心（預覽）**]。

2. 在 [私人連結中心 - 概觀] 中，選取 [與服務建立私人連線] 選項上的 [開始]。

3. 在 [**建立虛擬機器-基本**] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入您的*PrivateEndpoint*。 |
    | 區域 | 選取 [ **YourRegion**]。 |
    |||

4. 完成時，選取 [下一步:資源]。

5. 在 [**建立私人端點-資源**] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 [連線到我目錄中的 Azure 資源]。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [ **automationAccounts**]。 |
    | 資源 |選取*myAutomationAccount*|
    |目標子資源 |選取 [ *Webhook* ] 或 [ *DSCAndHybridWorker* ]，視您的案例而定。|
    |||

6. 完成時，選取 [下一步:組態]。

7. 在 [**建立私人端點-** 設定] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路**| |
    | 虛擬網路| 選取 [MyVirtualNetwork]。 |
    | 子網路 | 選取 [mySubnet]。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]。 |
    |私人 DNS 區域 |選取 *（新增） privatelink。 azure-automation.net* |
    |||

8. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

9. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

在**私人連結中心（預覽）** 中，選取 [**私人端點**] 以查看您的私人連結資源。

![自動化資源私人連結](./media/private-link-security/private-link-automation-resource.png)

選取要查看所有詳細資料的資源。 這會為您的自動化帳戶建立新的私人端點，並為其指派虛擬網路的私人 IP。 **連接狀態**會顯示為 [**已核准**]。

同樣地，會為狀態設定（agentsvc）和混合式 Runbook 背景工作角色作業執行時間（jrds）建立唯一的完整功能變數名稱（FQDN）。 系統會為每個使用者指派 VNet 中的個別 IP，且**線上狀態**會顯示為 [**已核准**]。

如果服務取用者具有自動化資源的 RBAC 許可權，他們就可以選擇自動核准方法。 在此情況下，當要求到達自動化提供者資源時，服務提供者不需要採取任何動作，且會自動核准連線。

## <a name="set-public-network-access-flags"></a>設定公用網路存取旗標

您可以設定自動化帳戶來拒絕所有公用設定，並只允許透過私人端點的連線，以進一步增強網路安全性。 如果您想要限制只能從 VNet 記憶體取自動化帳戶，而不允許從公用網際網路存取，您可以將屬性設定 `publicNetworkAccess` 為 `$false` 。

當 [**公用網路存取**] 設定設為時 `$false` ，只允許透過私人端點的連線，而且所有透過公用端點的連線都會遭到拒絕，並出現 unathorized 錯誤訊息和 HTTP 狀態401。 

下列 PowerShell 腳本會顯示如何 `Get` 和 `Set` 自動化帳戶層級的**公用網路存取**屬性：

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>DNS 組態

使用完整網域名稱 (FQDN) 作為連接字串的一部分來連線到私人連結資源時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 應該檢查您的 DNS 設定，並更新為使用您的私用端點進行連接。

與私人端點相關聯的網路介面包含設定 DNS 時所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 與私人 IP 位址。

您可以使用下列選項來設定私人端點的 DNS 設定：

* 使用主機檔案（僅建議用於測試）。 您可以使用虛擬機器上的主機檔案，先覆寫使用 DNS 進行名稱解析。

* 使用[私人 DNS 區域](../../dns/private-dns-privatednszone.md)。 您可以使用私人 DNS 區域來覆寫特定私人端點的 DNS 解析。 私人 DNS 區域可以連結至您的虛擬網路，以解析特定網域。 若要讓虛擬機器上的代理程式透過私人端點進行通訊，請建立私人 DNS 記錄做為 `privatelink.azure-automation.net` 。 將新的 DNS *a*記錄對應新增至私人端點的 IP。

* 使用您的 DNS 轉寄站（選擇性）。 您可以使用 DNS 轉寄站來覆寫特定私人連結資源的 DNS 解析。 如果您的 [DNS 伺服器](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)裝載在虛擬網路上，可以建立 DNS 轉送規則來使用私人 DNS 區域，以簡化所有私人連結資源的設定。

如需詳細資訊，請參閱[Azure 私人端點 DNS](../../private-link/private-endpoint-dns.md)設定。

## <a name="next-steps"></a>後續步驟

若要深入瞭解私用端點，請參閱[什麼是 Azure 私人端點？](../../private-link/private-endpoint-overview.md)。