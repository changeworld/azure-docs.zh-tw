---
title: Azure HANA 大型實例會透過 Azure 入口網站進行控制 |Microsoft Docs
description: 說明如何透過入口網站找出 Azure HANA 大型實例並與其互動的方式
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/31/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b03f3f93e8f829c6ab7dec965850117895a8b52d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882479"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>透過 Azure 入口網站控制 Azure HANA Large Instances

>[!NOTE]
>針對 Rev 4.2，請遵循 Azure 入口網站主題的 [管理 BareMetal 實例](../../../baremetal-infrastructure/workloads/sap/baremetal-infrastructure-portal.md) 中的指示。

本檔涵蓋了 [Hana 大型實例](./hana-overview-architecture.md) 在 [Azure 入口網站](https://portal.azure.com) 中的呈現方式，以及可透過 Azure 入口網站與為您部署的 hana 大型實例單位進行的活動。 Azure 入口網站中的 HANA 大型實例可見度是透過適用于 HANA 大型實例的 Azure 資源提供者所提供，其目前處於公開預覽狀態

## <a name="register-hana-large-instance-resource-provider"></a>註冊 HANA 大型實例資源提供者
通常，您用於 HANA 大型實例部署的 Azure 訂用帳戶會註冊給 HANA 大型實例資源提供者。 但是，如果您看不到部署的 HANA 大型實例單位，則應該在您的 Azure 訂用帳戶中註冊資源提供者。 註冊「HANA 大型實例」資源提供者的方式有兩種

### <a name="register-through-cli-interface"></a>透過 CLI 介面註冊
您必須登入 Azure 訂用帳戶，以透過 Azure CLI 介面用於 HANA 大型實例部署。 您可以使用下列命令 (重新 ) 註冊 HANA 大型執行個體提供者：
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

如需詳細資訊，請參閱[Azure 資源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)文章


### <a name="register-through-azure-portal"></a>註冊 Azure 入口網站
您可以 (重新 ) 透過 Azure 入口網站註冊 HANA 大型實例資源提供者。 您必須在 Azure 入口網站中列出您的訂用帳戶，然後按兩下用來將 HANA 大型實例單位部署 () 的訂用帳戶。 您位於訂用帳戶的 [總覽] 頁面中，選取 [資源提供者] （如下所示），並在搜尋視窗中輸入 "HANA"。 

![透過 Azure 入口網站註冊](./media/hana-li-portal/portal-register-hli-rp.png)

在顯示的螢幕擷取畫面中，已註冊資源提供者。 如果尚未註冊資源提供者，請按 [重新註冊] 或 [註冊]。

如需詳細資訊，請參閱[Azure 資源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)文章


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure 入口網站中的 HANA 大型實例單位顯示
提交 HANA 大型實例部署要求時，系統會要求您指定您要連接到 HANA 大型實例的 Azure 訂用帳戶。 建議使用您用來部署適用于 HANA 大型實例單位的 SAP 應用層的相同訂用帳戶。
當您第一次部署 HANA 大型實例時，會在您于「HANA 大型實例」 () 的部署要求中提交的 Azure 訂用帳戶中建立新的 [azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md) 。  新的資源群組會列出您已在特定訂用帳戶中部署的所有 HANA 大型實例單位。

若要尋找新的 Azure 資源群組，您可以在您的訂用帳戶中列出資源群組，方法是流覽 Azure 入口網站的左方流覽窗格

![醒目顯示 [資源群組] 選項的螢幕擷取畫面。](./media/hana-li-portal/portal-resource-group.png)

在您要列出的資源群組清單中，您可能需要篩選您用來部署 HANA 大型實例的訂用帳戶

![篩選 Azure 入口網站中的資源群組](./media/hana-li-portal/portal-filtering-subscription.png)

篩選正確的訂用帳戶之後，您仍然可能會有一長的資源群組清單。 尋找一個具有 **-Txxx** 的修正後，其中 "xxx" 是三位數，例如 **-T050**。 

當您找到資源群組時，請列出它的詳細資料。 您收到的清單看起來可能像這樣：

![Azure 入口網站中的？清單](./media/hana-li-portal/portal-hli-units-list.png)

所有列出的單位都代表已在您的訂用帳戶中部署的單一 HANA 大型實例單位。 在此情況下，您會看到8個不同的 HANA 大型實例單位，這些單位已部署在您的訂用帳戶中。

如果您在相同的 Azure 訂用帳戶下部署數個 HANA 大型實例租使用者，您會發現多個 Azure 資源群組 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看單一速度單位的屬性
在 HANA 大型實例單位清單中，您可以按一下單一單位，並取得單一 HANA 大型實例單位的詳細資料。 

在 [總覽] 畫面中，按一下 [顯示更多] 之後，您就會得到單元的簡報，看起來像這樣：

![顯示的一間單位總覽](./media/hana-li-portal/portal-show-overview.png)

查看所顯示的不同屬性，這些屬性看起來與 Azure VM 屬性幾乎不相同。 在左手邊的標頭中，它會顯示資源群組、Azure 區域、訂用帳戶名稱和識別碼，以及您新增的某些標記。 根據預設，HANA 大型實例單位不會指派任何標記。 在標頭右手邊，當部署完成時，會將單位的名稱列為 [已指派]。 系統會顯示作業系統以及 IP 位址。 就像 Vm 一樣，也會顯示具有 CPU 執行緒和記憶體數目的 HANA 大型實例單位類型。 不同的 HANA 大型實例單位有更多詳細資料，如下所示：

- [HLI 可用的 SKU](./hana-available-skus.md)
- [SAP HANA (大型執行個體) 儲存體架構](./hana-storage-architecture.md) 

右邊較低端的其他資料是 HANA 大型實例戳記的修訂。 可能的值包括：

- 修訂3
- 修訂4

修訂4是最新發行的 HANA 大型實例，其中包含 Azure Vm 與部署在修訂4戳記或資料列中的 HANA 大型實例單位之間的網路延遲大幅改進。
另一個非常重要的資訊是在總覽的右下角，以針對每個已部署的 HANA 大型實例單位自動建立的 Azure 鄰近放置群組的名稱來尋找。 部署裝載 SAP 應用層的 Azure Vm 時，必須參考此鄰近放置群組。 藉由使用與「HANA 大型實例」單位相關聯的 [azure 鄰近位置群組](../../co-location.md) ，您可確保 azure vm 會部署在與「Hana 大型實例」單位接近的位置。 使用鄰近位置群組的方式，在相同的 Azure 資料中心內找出與修訂4裝載的 HANA 大型實例單位相同的 Azure 資料中心中的 SAP 應用層，以 [獲得最佳的 sap 應用程式網路延遲](sap-proximity-placement-scenarios.md)。

標頭右側資料行中的額外欄位會告知 HANA 大型實例單位的電源狀態。

> [!NOTE]
> 電源狀態原因硬體單元是否開啟電源或關閉電源。 它不會提供正在啟動並執行之作業系統的相關資訊。 當您重新開機 HANA 大型實例單位時，您將會遇到一小段時間，其中的單位狀態會變更為 [ **開始** ] 狀態，並進入 [ **已啟動**] 狀態。 處於 [ **已啟動** ] 狀態表示作業系統正在啟動，或作業系統已完全啟動。 如此一來，在重新開機單元之後，您就無法預期只要狀態切換為 [ **已啟動**]，就會立即登入單位。
> 

如果您按 [查看更多]，則會顯示其他資訊。 另外還有一項資訊，是在中顯示「HANA 大型實例」戳記的修訂。 請參閱「 [Azure (大型實例上的 SAP Hana ](./hana-overview-architecture.md) 」一文，以瞭解不同的 HANA 大型實例戳記版本) 

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>檢查單一 HANA 大型實例單位的活動 
除了提供 HANA 大型實例單位的總覽之外，您還可以檢查特定單位的活動。 活動記錄可能看起來像這樣：

![Azure 入口網站中的流覽窗格](./media/hana-li-portal/portal-activity-list.png)

記錄的其中一個主要活動是重新開機單位。 列出的資料包含活動的狀態、觸發活動的時間戳記、觸發活動的訂用帳戶識別碼，以及觸發活動的 Azure 使用者。 

另一個正在記錄的活動是 Azure 中繼資料中的單位變更。 除了啟動重新開機之外，您還可以看到 **寫入 HANAInstances** 的活動。 這種類型的活動不會在 HANA 大型實例單位本身執行任何變更，而是在 Azure 中記錄單位中繼資料的變更。 在列出的案例中，我們已新增並刪除標記 (請參閱下一節) 。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>在 HANA 大型實例單位中新增和刪除 Azure 標記
另一個可能的情況是將 [標記](../../../azure-resource-manager/management/tag-resources.md) 新增至 HANA 大型實例單位。 指派標籤的方式與將標籤指派給 Vm 並無不同。 如同 Vm，標記存在於 Azure 中繼資料中，而針對 HANA 大型實例，與 Vm 的標記具有相同的限制。

刪除標記的運作方式與 Vm 相同。 這兩個活動（套用和刪除標記）都會列在特定 HANA 大型實例單位的活動記錄中。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>檢查 HANA 大型實例單位的屬性
區段 **屬性** 包含當您將實例傳遞給您時所取得的重要資訊。 它是一個區段，您可以在其中取得支援案例中所需的所有資訊，或是在設定儲存體快照集設定時所需的資訊。 如此一來，這一節就是您實例的資料集合、實例與 Azure 的連線能力，以及儲存體後端。 區段的頂端看起來像這樣：


![Azure 入口網站中的 [開始] 屬性的上半部](./media/hana-li-portal/portal-properties-top.png)

前幾個資料項目，您已在總覽畫面中看到。 但資料的重要部分是 ExpressRoute 線路識別碼，您可以在第一個部署的單元中取得此識別碼。 在某些支援案例中，您可能會被要求取得該資料。 螢幕擷取畫面底部會顯示重要的資料項目目。 顯示的資料是 NFS 儲存體標頭的 IP 位址，可將您的儲存體隔離到 HANA 大型實例堆疊中的 **租** 使用者。 當您編輯 [儲存體快照集備份的設定檔](./hana-backup-restore.md#set-up-storage-snapshots)時，也需要此 IP 位址。 

當您在 [屬性] 窗格中向下移動時，您會取得額外的資料，例如您的 HANA 大型實例單位的唯一資源識別碼，或指派給部署的訂用帳戶識別碼。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>透過 Azure 入口網站重新開機 HANA 大型實例單位
起始 Linux 作業系統的重新開機，在許多情況下，OS 無法順利完成重新開機。 若要強制重新開機，您必須開啟服務要求，讓 Microsoft 作業執行 HANA 大型實例單位的電源重新開機。 HANA 大型實例單位的電源重新開機功能現在已整合至 Azure 入口網站。 如同您在 HANA 大型實例單位的總覽部分，您會在 [資料] 區段上方看到 [重新開機] 按鈕

![Azure 入口網站中的重新開機步驟 #1](./media/hana-li-portal/portal-restart-first-step.png)

當您按下 [重新開機] 按鈕時，系統會詢問您是否真的想要重新開機單位。 當您按下 [是] 按鈕進行確認時，該單位將會重新開機。

> [!NOTE]
> 在重新開機過程中，您將會遇到一小段時間，讓單元的狀態變更 **為「開始」** 進入 [ **已啟動**] 狀態。 處於 [ **已啟動** ] 狀態表示作業系統正在啟動，或作業系統已完全啟動。 如此一來，在重新開機單元之後，您就無法預期只要狀態切換為 [ **已啟動**]，就會立即登入單位。

> [!IMPORTANT]
> 視您的 HANA 大型實例單位中的記憶體數量而定，重新開機和重新開機硬體和作業系統最多可能需要一小時的時間。


## <a name="open-a-support-request-for-hana-large-instances"></a>開啟 HANA 大型實例的支援要求
在 HANA 大型實例單位的 Azure 入口網站顯示中，您也可以特別針對 HANA 大型實例單位建立支援要求。 當您遵循連結 **新的支援要求** 時 

![在 Azure 入口網站中起始服務要求步驟 #1](./media/hana-li-portal/portal-initiate-support-request.png)

為了取得下一個畫面所列 SAP Hana 大型實例的服務，您可能需要選取 [所有服務]，如下所示

![選取 Azure 入口網站中的所有服務](./media/hana-li-portal/portal-create-service-request.png)

在服務清單中，您可以找到 **SAP Hana 大型實例** 的服務。 當您選擇該服務時，您可以選取特定的問題類型，如下所示：


![在 Azure 入口網站中選取問題類別](./media/hana-li-portal/portal-select-problem-class.png)

在每個不同的問題類型下，您會看到您需要選取的問題子類型，以進一步描述您的問題。 選取子類型之後，您現在可以命名主體。 完成選取程式之後，您可以移至建立的下一個步驟。 在 [ **解決方案** ] 區段中，您會指向「HANA 大型實例」的相關檔，其可能會提供您問題解決方案的指標。 如果您在建議的檔中找不到問題的解決方案，請移至下一個步驟。 在下一個步驟中，系統會詢問您是否有 Vm 或 HANA 大型實例單位的問題。 此資訊有助於將支援要求導向至正確的專家。 

![Azure 入口網站中的支援案例詳細資料](./media/hana-li-portal/portal-support-request-details.png)

當您回答問題並提供其他詳細資料時，您可以前往下一個步驟，以審查支援要求並提交。

## <a name="next-steps"></a>後續步驟

- [如何監視 Azure 上的 SAP HANA (大型執行個體)](./troubleshooting-monitoring.md)
- [從 HANA 端進行監視和疑難排解](./hana-monitor-troubleshoot.md)