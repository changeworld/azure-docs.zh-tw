---
title: Azure HANA 大型實例通過 Azure 門戶控制 |微軟文檔
description: 描述如何通過門戶識別和與 Azure HANA 大型實例進行交互
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099823"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>透過 Azure 入口網站控制 Azure HANA Large Instances
本文檔介紹了在[Azure 門戶](https://portal.azure.com)中顯示[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)的方式，以及哪些活動可以通過 Azure 門戶進行，其中部署了 HANA 大型實例單元。 Azure 門戶中 HANA 大型實例的可見度通過 HANA 大型實例的 Azure 資來源提供者提供，該提供程式當前處於公共預覽版中

## <a name="register-hana-large-instance-resource-provider"></a>註冊 HANA 大型實例資來源提供者
通常，用於 HANA 大型實例部署的 Azure 訂閱為 HANA 大型實例資來源提供者註冊。 但是，如果您看不到已部署的 HANA 大型實例單元，則應在 Azure 訂閱中註冊資來源提供者。 註冊 HANA 大型實例資來源提供者有兩種方法

### <a name="register-through-cli-interface"></a>通過 CLI 介面註冊
您需要登錄到 Azure 訂閱，該訂閱通過 Azure CLI 介面用於 HANA 大型實例部署。 您可以使用以下命令（重新）註冊 HANA 大型實例提供程式：
    
    az provider register --namespace Microsoft.HanaOnAzure

有關詳細資訊，請參閱 Azure[資來源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)的文章


### <a name="register-through-azure-portal"></a>通過 Azure 門戶註冊
您可以通過 Azure 門戶（重新）註冊 HANA 大型實例資來源提供者。 您需要在 Azure 門戶中列出訂閱，並按兩下訂閱，該訂閱用於部署 HANA 大型實例單元。 在訂閱的概述頁中，選擇如下所示的"資來源提供者"，並在搜索視窗中鍵入"HANA"。 

![通過 Azure 門戶註冊 HLI RP](./media/hana-li-portal/portal-register-hli-rp.png)

在顯示的螢幕截圖中，資來源提供者已註冊。 如果資來源提供者尚未註冊，請按"重新註冊"或"註冊"。

有關詳細資訊，請參閱 Azure[資來源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)的文章


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>在 Azure 門戶中顯示 HANA 大型實例單元
提交 HANA 大型實例部署請求時，系統將要求您指定要連接到 HANA 大型實例的 Azure 訂閱。 建議使用與您使用的相同訂閱來部署針對 HANA 大型實例單元的 SAP 應用程式層。
部署第一個 HANA 大型實例時，將在您在 HANA 大型實例的部署請求中提交的 Azure 訂閱中創建新的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。  新資源組將列出在特定訂閱中部署的所有 HANA 大型實例單元。

為了查找新的 Azure 資源組，可以通過流覽 Azure 門戶的左側功能窗格來列出訂閱中的資源組

![Azure 門戶中的功能窗格](./media/hana-li-portal/portal-resource-group.png)

在資源組清單中，您要列出，您可能需要篩選以前部署 HANA 大型實例的訂閱

![在 Azure 門戶中篩選資源組](./media/hana-li-portal/portal-filtering-subscription.png)

篩選到正確的訂閱後，您仍可能有一長串的資源組。 查找一個後修復 **-Txxx，"xxx"** 是三位數位，如 **-T050**。 

找到資源組時，請列出資源組的詳細資訊。 您收到的清單可能如下所示：

![Azure 門戶中的 HLI 清單](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有單元都表示已在訂閱中部署的單個 HANA 大型實例單元。 在這種情況下，您查看了 8 個不同的 HANA 大型實例單元，這些單元已部署在訂閱中。

如果在同一 Azure 訂閱下部署了多個 HANA 大型實例租戶，則會發現多個 Azure 資源組 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看單個 HLI 單元的屬性
在 HANA 大型實例單元清單中，您可以按一下單個單元並獲取單個 HANA 大型實例單元的詳細資訊。 

在概述螢幕中，按一下"顯示更多"後，您會收到該裝置的演示文稿，如下所示：

![顯示 HLI 單元的概述](./media/hana-li-portal/portal-show-overview.png)

查看顯示的不同屬性，這些屬性看起來與 Azure VM 屬性幾乎沒什麼不同。 在左側標頭上，它顯示資源組、Azure 區域、訂閱名稱和 ID 以及添加的某些標記。 預設情況下，HANA 大型實例單元沒有分配標記。 在標頭的右側，單元的名稱在部署完成後列為已分配的名稱。 作業系統以及 IP 位址都顯示。 與 VM 一樣，也顯示了具有 CPU 執行緒數和記憶體的 HANA 大型實例單元類型。 有關不同 HANA 大型實例單元的更多詳細資訊，如下所示：

- [適用於 HLI 的可用 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (大型執行個體) 儲存體架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

右側的其他資料是 HANA 大型實例戳的修訂版。 可能的值包括：

- 修訂版 3
- 修訂版 4

修訂版 4 是 HANA 大型實例發佈的最新體系結構，在 Azure VM 和 HANA 大型實例單元之間的網路延遲方面有重大改進，部署在版本 4 戳或行中。
另一個非常重要的資訊位於概覽的右下角，其名稱為 Azure 鄰近放置組，該組會自動為每個已部署的 HANA 大型實例單元創建。 在部署託管 SAP 應用程式層的 Azure VM 時，需要引用此鄰近放置組。 通過使用與 HANA 大型實例單元關聯的[Azure 鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，可以確保 Azure VM 部署在 HANA 大型實例單元附近。 Azure 鄰近放置組仲介紹了如何使用鄰近放置組查找與修訂版 4 託管 HANA 大型實例單元位於同一 Azure 資料中心中的 SAP 應用程式層的方式，[以便使用 SAP 應用程式實現最佳網路延遲](sap-proximity-placement-scenarios.md)。

標題右列中的附加欄位將通知 HANA 大型實例單元的電源狀態。

> [!NOTE]
> 電源狀態原因硬體單元是開機還是關。 它不提供有關作業系統正在啟動和運行的資訊。 重新開機 HANA 大型實例單元時，您會遇到一小段時間，設備的狀態更改為 **"開始**"進入 **"已啟動"** 狀態。 處於 **"已啟動"** 狀態意味著作業系統正在啟動或作業系統已完全啟動。 因此，重新開機設備後，您不能指望在狀態切換到 **"已啟動"** 時立即登錄到設備。
> 

如果按"查看詳細資訊"，將顯示其他資訊。 另外一個資訊是顯示 HANA 大型實例戳的修訂版，該版本已部署在其中。 有關 HANA 大型實例圖章的不同修訂版本，請參閱[Azure（大型實例）上的 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)文章

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>檢查單個 HANA 大型實例單元的活動 
除了概述 HANA 大型實例單元外，您還可以檢查特定單元的活動。 活動日誌可能如下所示：

![Azure 門戶中的功能窗格](./media/hana-li-portal/portal-activity-list.png)

記錄的主要活動之一是重新開機設備。 列出的資料包括活動的狀態、觸發活動的時間戳記、觸發活動的訂閱 ID 以及觸發活動的 Azure 使用者。 

正在記錄的另一個活動是 Azure 中繼資料中單元的更改。 除了重新開機啟動之外，您還可以看到**寫入 HANA實例**的活動。 這種類型的活動對 HANA 大型實例單元本身不執行任何更改，而是記錄對 Azure 中單元的中繼資料的更改。 在列出的案例中，我們添加並刪除了一個標記（請參閱下一節）。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>向 HANA 大型實例單元添加和刪除 Azure 標記
另一種可能性是向 HANA 大型實例單元添加[標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)。 標記的分配方式與向 VM 分配標記的方式不同。 與 VM 一樣，標記存在於 Azure 中繼資料中，對於 HANA 大型實例，標記具有與 VM 標記相同的限制。

刪除標記的工作方式與 VM 相同。 應用和刪除標記的活動都將列在特定 HANA 大型實例單元的活動日誌中。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>檢查 HANA 大型實例單元的屬性
**屬性**部分包括將實例移交給您時獲取的重要資訊。 在其中，您可以獲取在支援情況下可能需要的所有資訊，或者在設置存儲快照配置時需要的資訊。 因此，本節是實例周圍的資料集合、實例與 Azure 的連接以及存儲後端。 該部分的頂部如下所示：


![Azure 門戶中 HLI 屬性的頂部部分](./media/hana-li-portal/portal-properties-top.png)

在概述螢幕中已經看到的前幾個資料項目。 但資料的一個重要部分是 ExpressRoute 電路 ID，當第一個部署的單元被移交時，您得到了該 ID。 在某些支援情況下，可能會要求您提供該資料。 一個重要的資料條目顯示在螢幕截圖的底部。 顯示的資料是 NFS 存儲頭的 IP 位址，該位址將存儲隔離到 HANA 大型實例堆疊中的**租戶**。 當您編輯[存儲快照備份的設定檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)時，也需要此 IP 位址。 

在屬性窗格中向下滾動時，您將獲得其他資料，如 HANA 大型實例單元的唯一資源識別碼 或分配給部署的訂閱 ID。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>通過 Azure 門戶重新開機 HANA 大型實例單元
啟動 Linux 作業系統的重新開機時，作業系統無法成功完成重新開機。 要強制重新開機，您需要打開服務請求，讓 Microsoft 操作執行 HANA 大型實例單元的電源重新開機。 HANA 大型實例單元的電源重新開機功能現在集成到 Azure 門戶中。 當您在 HANA 大型實例單元的概述部分中時，您會看到在資料部分頂部重新開機的按鈕

![重新開機 Azure 門戶中的步驟#1](./media/hana-li-portal/portal-restart-first-step.png)

按下重新開機按鈕時，系統會詢問您是否確實要重新開機設備。 當您通過按下按鈕"是"進行確認時，設備將重新開機。

> [!NOTE]
> 在重新開機過程中，您將經歷一小段時間，設備的狀態更改為 **"開始**"移動到 **"已啟動"** 狀態。 處於 **"已啟動"** 狀態意味著作業系統正在啟動或作業系統已完全啟動。 因此，重新開機設備後，您不能指望在狀態切換到 **"已啟動"** 時立即登錄到設備。

> [!IMPORTANT]
> 根據 HANA 大型實例單元中的記憶體量，重新開機和重新開機硬體和作業系統最多可能需要一個小時


## <a name="open-a-support-request-for-hana-large-instances"></a>打開 HANA 大型實例的支援請求
在 HANA 大型實例單元的 Azure 門戶顯示中，還可以為 HANA 大型實例單元專門創建支援請求。 當您關注連結**新支援請求時** 

![在 Azure 門戶中啟動服務請求步驟#1](./media/hana-li-portal/portal-initiate-support-request.png)

為了獲得下一個螢幕中列出的 SAP HANA 大型實例的服務，您可能需要選擇如下所示的"所有服務"

![選擇 Azure 門戶中的所有服務](./media/hana-li-portal/portal-create-service-request.png)

在服務清單中，可以找到服務**SAP HANA 大型實例**。 選擇該服務時，可以選擇特定問題類型，如下所示：


![在 Azure 門戶中選擇問題類](./media/hana-li-portal/portal-select-problem-class.png)

在每個不同的問題類型下，您都會選擇一系列問題子類型，以便進一步描述問題的特徵。 選擇子類型後，您現在可以為主題命名。 完成選擇過程後，可以轉到創建的下一步。 在 **"解決方案"** 部分中，您可以指向有關 HANA 大型實例的文檔，這可能提供指向問題解決方案的指標。 如果在建議的文檔中找不到問題的解決方案，請轉到下一步。 在下一步中，系統將詢問問題是 VM 還是 HANA 大型實例單元。 此資訊有助於將支援請求定向到正確的專家。 

![Azure 門戶中支援案例的詳細資訊](./media/hana-li-portal/portal-support-request-details.png)

當您回答問題並提供其他詳細資訊時，您可以執行下一步以查看支援請求並提交支援請求。

## <a name="next-steps"></a>後續步驟

- [如何監視 Azure 上的 SAP HANA (大型執行個體)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [從 HANA 端進行監視和疑難排解](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

