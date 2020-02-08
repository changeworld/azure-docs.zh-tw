---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tanmaygore
ms.openlocfilehash: 215057640dd08d9ea524d8f6b3bed8b03a8b5b8c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068409"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>將 IaaS 資源從傳統部署模型移轉至 Azure Resource Manager
首先，務必了解基礎結構即服務 (IaaS) 資源上資料平面與管理平面作業之間的差異。

* 「管理/控制平面」說明進入管理/控制平面或 API 以便修改資源的呼叫。 例如，建立 VM、重新啟動 VM 以及將虛擬網路更新成使用新子網路等作業皆可管理執行中的資源。 它們並不直接影響對 VM 的連線。
* 「資料平面」(應用程式) 說明應用程式本身的執行階段，並牽涉到與不通過 Azure API 的執行個體進行互動。 例如，不論是存取您的網站，還是從執行中的 SQL Server 或 MongoDB 伺服器提取資料，都會被視為資料平面或應用程式互動。 其他範例包含從儲存體帳戶複製 Blob，以及存取公用 IP 位址，以使用遠端桌面通訊協定 (RDP) 或安全殼層 (SSH) 進入虛擬機器。 這些作業會讓應用程式繼續跨計算、網路和儲存體執行。

傳統部署模型與 Resource Manager 堆疊之間的資料平面相同。 差異在於移轉過程中，Microsoft 會將資源的表示法從傳統部署模型轉譯為 Resource Manager 堆疊中的表示法。 因此，您必須使用新的工具、API 和 SDK 來管理 Resource Manager 堆疊中資源。

![顯示管理/控制平面與資料平面之間差異的圖表](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> 在一些移轉案例中，Azure 平台會將您的虛擬機器停止、解除配置及重新啟動。 這會造成短暫的資料平面停機時間。
>

## <a name="the-migration-experience"></a>移轉體驗
在開始移轉之前：

* 確定您要移轉的資源未使用任何不支援的功能或組態。 通常，平台會偵測這些問題並產生錯誤。
* 如果您有不在虛擬網路中的 VM，則會在準備作業過程中，將這些 VM 停止並解除配置。 如果您不想失去公用 IP 位址，請在觸發準備作業之前，先考慮如何保留 IP 位址。 如果 VM 位於虛擬網路中，它們就不會被停止並解除配置。
* 規劃非上班時間的移轉，以便因應移轉期間可能發生的任何非預期失敗。
* 使用 PowerShell、命令列介面 (CLI) 命令或 REST API 來下載現行的 VM 組態，以便在完成準備步驟之後能夠更容易進行驗證。
* 先更新用來處理 Resource Manager 部署模型的自動化和作業化指令碼，再開始移轉。 當資源處於已準備就緒狀態時，您可以視需要執行 GET 作業。
* 在移轉完成之後，評估傳統部署模型中 IaaS 資源上設定的角色型存取控制 (RBAC) 原則並備妥計劃。

移轉工作流程如下：

![顯示移轉工作流程的圖表](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> 下列各節描述的作業都是等冪的。 如果您有不支援的功能或組態錯誤以外的任何問題，請重新嘗試準備、中止或認可作業。 Azure 會再次嘗試此動作。
>
>

### <a name="validate"></a>Validate
驗證作業是移轉程序的第一個步驟。 此步驟的目標是要分析您希望在傳統部署模型中移轉之資源的狀態。 此作業會評估資源是否能夠移轉 (成功或失敗)。

您可選取要進行移轉驗證的虛擬網路或雲端服務 (如果不在虛擬網路中)。 如果資源不能夠移轉，Azure 會列出其原因。

#### <a name="checks-not-done-in-the-validate-operation"></a>不在驗證作業中完成的檢查

驗證作業只會分析傳統部署模型中資源的狀態。 該作業可檢查因為傳統部署模型中的各種組態而造成的所有失敗和不支援的案例。 它不可能檢查 Azure Resource Manager 堆疊在移轉期間可能強加於資源的所有問題。 只有資源在下一個移轉步驟 (準備作業) 中進行轉換時，才會檢查這些問題。 下表列出所有未在驗證作業中檢查的問題：


|不在驗證作業中的網路功能檢查|
|-|
|具有 ER 和 VPN 閘道的虛擬網路。|
|處於中斷連線狀態的虛擬網路閘道連線。|
|所有 ER 線路都已預先移轉至 Azure Resource Manager 堆疊。|
|Azure Resource Manager 配額會檢查網路資源。 例如：靜態公用 IP、動態公用 IP、負載平衡器、網路安全性群組、路由表和網路介面。 |
| 在部署和虛擬網路中所有負載平衡器規則都有效。 |
| 相同虛擬網路中已停止並解除配置的 VM 之間有衝突的私人 IP。 |

### <a name="prepare"></a>準備
準備作業是移轉程序的第二個步驟。 這個步驟的目標是要模擬將 IaaS 資源從傳統部署模型轉換為 Resource Manager 資源的轉換過程。 此外，準備作業會以並排方式呈現此過程以供您用視覺化方式檢視。

> [!NOTE] 
> 傳統部署模型中的資源不會在此步驟期間進行修改。 如果您嘗試移轉，這是安全的執行步驟。 

您可選取要為移轉做準備的虛擬網路或雲端服務 (如果它不是虛擬網路)。

* 如果資源不能進行移轉，Azure 會停止移轉程序並列出準備作業失敗的原因。
* 如果資源能夠進行移轉，Azure 會鎖定進行移轉之資源的管理平面作業。 例如，您會無法將資料磁碟新增至進行移轉的 VM。

接著，Azure 會開始將移轉中資源的中繼資料從傳統部署模型移轉至 Resource Manager。

準備作業完成之後，您將可以選擇在傳統部署模型和 Resource Manager 中將資源視覺化。 Azure 平台會為傳統部署模型中的每個雲端服務，建立一個採用 `cloud-service-name>-Migrated`模式的資源群組名稱。

> [!NOTE]
> 不可能選取為已移轉資源建立之資源群組的名稱 (也就是 "-Migrated")。 不過，完成移轉之後，您就可以使用 Azure Resource Manager 的移動功能，將資源移至您想要的任何資源群組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../articles/resource-group-move-resources.md)。

以下兩個螢幕擷取畫面顯示成功準備作業之後的結果。 第一個螢幕擷取畫面顯示包含原始雲端服務的資源群組。 第二個螢幕擷取畫面顯示新的 "-Migrated" 資源群組，其中包含對等的 Azure Resource Manager 資源。

![顯示原始雲端服務的螢幕擷取畫面](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![顯示準備作業中 Azure Resource Manager 資源的螢幕擷取畫面](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

以下是準備階段完成之後，資源的幕後外觀。 請注意，資料平面中的資源相同。 它會以管理平面 (傳統部署模型) 和控制平面 (Resource Manager) 表示。

![準備階段的圖表](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> 在這個移轉階段中，不在傳統部署模型的虛擬網路中的 VM 會停止並解除配置。
>

### <a name="check-manual-or-scripted"></a>檢查 (手動或透過指令碼)
在檢查步驟中，您可以選擇使用您稍早下載的組態來驗證移轉是否正確無誤。 或者，您也可以登入入口網站並抽樣檢查屬性和資源，以驗證中繼資料移轉是否正確無誤。

如果您移轉的是虛擬網路，則虛擬機器的大部分組態都不會重新啟動。 對於這些 VM 上的應用程式，您可以確認應用程式是否仍在執行中。

您可以測試您的監視和作業指令碼，以查看 VM 是否如預期方式運作，以及更新後的指令碼是否正常運作。 當資源處於已準備就緒的狀態時，只支援 GET 作業。

系統並未設定您必須在哪個期限之前認可移轉。 您可以在此狀態停留任何長度的時間。 不過，這些資源的管理平面將會處於鎖定狀態，直到您進行中止或認可為止。

如果您發現任何問題，您一律可以中止移轉，並返回傳統部署模型。 在您返回之後，Azure 會開放對資源的平面管理作業，讓您可以在傳統部署模型中繼續對這些 VM 進行一般作業。

### <a name="abort"></a>中止
如果您要將變更還原至傳統部署模型並停止移轉，這是選擇性步驟。 此作業會刪除您資源的 Resource Manager 中繼資料 (在準備步驟中建立的)。 

![中止步驟的圖表](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> 在您觸發認可作業之後，就無法完成此作業。     
>

### <a name="commit"></a>Commit
完成驗證之後，您便可以認可移轉。 資源不會再出現於傳統部署模型中，而只有在 Resource Manager 部署模型中才能使用這些資源。 只能在新入口網站中管理已移轉的資源。

> [!NOTE]
> 這是一種等冪作業。 如果失敗，請重試此作業。 如果持續失敗，請建立支援票證，或在 Microsoft Q 上建立論壇[& a](https://docs.microsoft.com/answers/index.html)
>
>

![認可步驟的圖表](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>移轉流程圖

以下流程圖顯示如何繼續進行移轉：

![Screenshot that shows the migration steps](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>從傳統部署模型轉譯成 Resource Manager 資源
下表提供資源的傳統部署模型和 Resource Manager 表示法。 目前不支援其他功能和資源。

| 傳統表示法 | Resource Manager 表示法 | 注意 |
| --- | --- | --- |
| 雲端服務名稱 |DNS 名稱 |在移轉期間，會以命名樣式 `<cloudservicename>-migrated`為每個雲端服務建立新的資源群組。 此資源群組包含您的所有資源。 雲端服務名稱會成為與公用 IP 位址關聯的 DNS 名稱。 |
| 虛擬機器 |虛擬機器 |VM 特定屬性會原封不動地移轉過去。 有些 osProfile 資訊 (例如電腦名稱) 不會儲存在傳統部署模型中，因此移轉後會保留空白。 |
| 連接至 VM 的磁碟資源 |連接至 VM 的隱含磁碟 |在 Resource Manager 部署模型中，並未將磁碟塑造成最上層資源。 這些磁碟是被當作 VM 底下的隱含磁碟來移轉。 目前只支援連接至 VM 的磁碟。 Resource Manager VM 現在可以使用傳統部署模型中的儲存體帳戶，這可讓您不須進行任何更新，即可輕鬆移轉磁碟。 |
| VM 擴充功能 |VM 擴充功能 |所有資源擴充功能 (XML 擴充功能除外) 都會從傳統部署模型移轉出來。 |
| 虛擬機器憑證 |Azure 金鑰保存庫中的憑證 |如果雲端服務包含服務憑證，移轉作業就會為每項雲端服務建立新的 Azure Key Vault，然後將憑證移至該金鑰保存庫中。 VM 將會更新為參照該金鑰保存庫中的憑證。 <br><br> 請勿刪除 Key Vault。 這會導致 VM 進入失敗狀態。 |
| WinRM 組態 |osProfile 下的 WinRM 組態 |「Windows 遠端管理」組態在移轉過程中會原封不動地移過去。 |
| 可用性設定組屬性 |可用性設定組資源 | 可用性設定組規格是傳統部署模型中 VM 上的屬性。 在移轉過程中，可用性設定組會變成最上層資源。 不支援下列組態：每個雲端服務有多個可用性設定組，或是一或多個可用性設定組帶有不屬於雲端服務中任何可用性設定組的 VM。 |
| VM 上的網路組態 |主要網路介面 |移轉之後，VM 上的網路組態就會顯示為主要網路介面資源。 VM 如果不在虛擬網路中，其內部 IP 位址在移轉期間將會變更。 |
| VM 上的多個網路介面 |網路介面 |如果 VM 有多個相關聯的網路介面，則每個網路介面及所有的屬性都會在移轉過程中成為最上層資源。 |
| 負載平衡的端點集 |負載平衡器 |在傳統部署模型中，平台已為每個雲端服務指派一個隱含的負載平衡器。 在移轉期間，會建立新的負載平衡器資源，而負載平衡端點集則會成為負載平衡器規則。 |
| 傳入的 NAT 規則 |傳入的 NAT 規則 |在移轉期間，VM 上定義的輸入端點會轉換成負載平衡器下的輸入網路位址轉譯規則。 |
| VIP 位址 |具 DNS 名稱的公用 IP 位址 |虛擬 IP 位址會變成公用 IP 位址並與負載平衡器產生關聯。 有指派給虛擬 IP 的輸入端點時，才可移轉該虛擬 IP。 |
| 虛擬網路 |虛擬網路 |虛擬網路會與其所有屬性一起移轉至 Resource Manager 部署模型。 將會建立名為 `-migrated`的新資源群組。 |
| 保留的 IP |搭配靜態配置方法的公用 IP 位址 |與負載平衡器關聯的保留 IP 會隨著雲端服務或虛擬機器的移轉一併移轉。 目前不支援移轉未關聯的保留 IP。 |
| 每一個 VM 的公用 IP 位址 |搭配動態配置方法的公用 IP 位址 |與 VM 關聯的公用 IP 位址會轉換成公用 IP 位址資源，且配置方法會設定為靜態。 |
| NSG |NSG |在移轉至 Resource Manager 部署模型的過程中，會複製與子網路關聯的網路安全性群組。 在移轉期間不會移除傳統部署模型中的 NSG。 不過，在移轉進行時，會封鎖 NSG 的管理平面作業。 |
| DNS 伺服器 |DNS 伺服器 |與虛擬網路或 VM 關聯的 DNS 伺服器，會在對應的資源移轉過程中，與其所有屬性一起移轉。 |
| UDR |UDR |在移轉至 Resource Manager 部署模型的過程中，會複製與子網路關聯的使用者定義路由。 在移轉期間不會移除傳統部署模型中的 UDR。 不過，移轉進行時，會封鎖 UDR 的管理平面作業。 |
| VM 網路組態上的 IP 轉送屬性 |NIC 上的 IP 轉送屬性 |在移轉期間，VM 上的 IP 轉送屬性會轉換成網路介面上的屬性。 |
| 具有多個 IP 的負載平衡器 |具有多個公用 IP 資源的負載平衡器 |與負載平衡器關聯的每個公用 IP 在移轉後都會轉換成公用 IP 資源，並與負載平衡器產生關聯。 |
| VM 上的內部 DNS 名稱 |NIC 上的內部 DNS 名稱 |在移轉期間，VM 的內部 DNS 尾碼會移轉至 NIC 上名為 "InternalDomainNameSuffix" 的唯讀屬性。 尾碼在移轉後保持不變，VM 解析應該會像之前一樣繼續運作。 |
| 虛擬網路閘道 |虛擬網路閘道 |不變更虛擬網路閘道內容下進行移轉。 與閘道相關聯的 VIP 也不會變更。 |
| 區域網路網站 |區域網路閘道 |不變更區域網路網站的內容下移轉到稱為本機網路閘道的新資源。 這表示內部部署位址首碼與遠端閘道的 IP。 |
| 連線參考 |Connection |閘道與網路組態中的區域網路網站之間的連線參考會以稱為連線的新資源表示。 網路組態檔中連線參考的所有屬性都會在不變更下複製到連線資源。 傳統部署模型中虛擬網路之間連線能力的實現方式為建立兩個區域網路網站的 IPsec 通道以代表虛擬網路。 這會在 Resource Manager 模型中轉換為虛擬網路對虛擬網路連線類型，而不需要區域網路閘道。 |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>移轉之後對自動化及工具的變更
在將資源從「傳統」部署模型移轉至 Resource Manager 部署模型的過程中，您必須更新現有的自動化或工具，以確保它在移轉之後仍可繼續運作。
