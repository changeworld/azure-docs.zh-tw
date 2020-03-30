---
title: Azure 遷移設備常見問題解答
description: 獲取有關 Azure 遷移設備的常見問題的解答。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2ef7de3b31cb11a71ec9379232fc5ff1022cf666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336981"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure 遷移設備：常見問題

本文回答了有關 Azure 遷移設備的常見問題。 如果您有其他問題，請檢查以下資源：

- 有關 Azure 遷移的[一般問題](resources-faq.md)
- 有關[發現、評估和依賴項視覺化](common-questions-discovery-assessment.md)的問題
- 有關[伺服器遷移](common-questions-server-migration.md)的問題
- 在[Azure 遷移論壇](https://aka.ms/AzureMigrateForum)中回答問題

## <a name="what-is-the-azure-migrate-appliance"></a>什麼是 Azure 遷移設備？

Azure 遷移設備是 Azure 遷移：伺服器評估工具用於發現和評估本機伺服器的羽量級設備。 Azure 遷移：伺服器遷移工具還使用該設備對本地 VMware VM 進行無代理遷移。

以下是有關 Azure 遷移設備的詳細資訊：

- 設備在本地部署為 VM 或物理電腦。
- 設備發現本地電腦，並不斷向 Azure 遷移發送電腦中繼資料和效能資料。
- 設備探索無須代理程式。 未在已發現的電腦上安裝任何內容。

[瞭解有關產品詳細資訊](migrate-appliance.md)。

## <a name="how-does-the-appliance-connect-to-azure"></a>設備如何連接到 Azure？

設備可以通過 Internet 或將 Azure ExpressRoute 與公共/微軟對等互連連接。

## <a name="does-appliance-analysis-affect-performance"></a>設備分析會影響性能嗎？

Azure 連續遷移本地電腦的設備設定檔，以測量效能資料。 此分析對已分析的電腦幾乎沒有性能影響。

## <a name="can-i-harden-the-appliance-vm"></a>我可以強化設備 VM 嗎？

使用下載的範本創建設備 VM 時，如果保留 Azure 遷移設備所需的通信和防火牆規則，則可以向範本添加元件（例如防病毒）。

## <a name="what-network-connectivity-is-required"></a>需要哪些網路連接？

有關 Azure 遷移設備的網路連接要求的資訊，請參閱以下文章：

- **VMware 評估** [：URL 訪問](migrate-appliance.md#url-access)和[埠訪問](migrate-support-matrix-vmware.md#port-access)
- **VMware 無代理遷移**[：URL 訪問](migrate-appliance.md#url-access)和[埠訪問](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Hyper-V 評估** [：URL 訪問](migrate-appliance.md#url-access)和[埠訪問](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>產品收集哪些資料？

有關 Azure 遷移設備在 VM 上收集的資料的資訊，請參閱以下文章：

- **VMware VM**：[查看](migrate-appliance.md#collected-data---vmware)收集的資料。 [
- **Hyper-V VM**：[查看](migrate-appliance.md#collected-data---hyper-v)收集的資料。

## <a name="how-is-data-stored"></a>儲存資料的方式

Azure 遷移設備收集的資料存儲在創建 Azure 遷移專案的 Azure 位置。

以下是有關如何存儲資料的詳細資訊：

- 收集的資料安全地存儲在 Microsoft 訂閱中。 刪除 Azure 遷移專案時，將刪除資料。 存儲由 Azure 遷移處理。 不能為收集的資料專門選擇存儲帳戶。
- 如果使用[依賴項視覺化](concepts-dependency-visualization.md)，則收集的資料將存儲在 Azure 訂閱中創建的 Azure 日誌分析工作區中。 刪除訂閱中的日誌分析工作區時，資料將被刪除。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>在連續分析期間上傳了多少資料？

發送到 Azure 遷移的資料量取決於多個參數。 例如，具有 10 台電腦（每個電腦有一個磁片和一個 NIC）的 Azure 遷移專案每天發送大約 50 MB 的資料。 此值是近似值;實際值因磁片和 NIC 的資料點數而異。 如果電腦、磁片或 NIC 的數量增加，則發送的資料的增加是非線性的。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>資料是否在靜態和傳輸中加密？

是，對於這兩個：

- 中繼資料通過 HTTPS 通過互聯網安全地發送到 Azure 遷移服務。
- 中繼資料存儲在 Azure[宇宙](../cosmos-db/database-encryption-at-rest.md)資料庫中，存儲在 Microsoft 訂閱中的[Azure Blob 存儲](../storage/common/storage-service-encryption.md)中。 中繼資料在靜態時進行加密以進行存儲。
- 依賴項分析的資料也在傳輸過程中（通過安全 HTTPS）進行加密。 它存儲在訂閱中的日誌分析工作區中。 資料在靜態時進行加密，以便依賴項分析。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>設備如何連接到 vCenter 伺服器？

這些步驟描述了設備如何連接到 VMware vCenter 伺服器：

1. 設備使用設置設備時提供的憑據連接到 vCenter 伺服器（埠 443）。
2. 設備使用 VMware PowerCLI 查詢 vCenter 伺服器來收集有關 vCenter 伺服器管理的 VM 的中繼資料。
3. 設備收集有關 VM（內核、記憶體、磁片、NIC）和每個 VM 過去一個月的性能歷史記錄的配置資料。
4. 收集的中繼資料將發送到 Azure 遷移：伺服器評估工具（通過互聯網通過 HTTPS 進行評估）。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure 遷移設備是否可以連接到多個 vCenter 伺服器？

否。 [Azure 遷移設備和](migrate-appliance.md)vCenter 伺服器之間存在一對一映射。 要發現多個 vCenter Server 實例上的 VM，必須部署多個設備。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure 遷移專案是否可以具有多個設備？
專案可以附加多個設備。 但是，設備只能與一個專案關聯。 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>使用設備可以發現多少個 VM 或伺服器？

您最多可以發現 10，000 個 VMware VM、多達 5，000 台超虛擬機器，以及最多 250 台物理伺服器，只需一台設備。 如果本地環境中有更多的電腦，請閱讀有關[縮放 Hyper-V 評估](scale-hyper-v-assessment.md)、[縮放 VMware 評估](scale-vmware-assessment.md)以及[縮放物理伺服器評估](scale-physical-assessment.md)。

## <a name="can-i-delete-an-appliance"></a>我可以刪除設備嗎？

目前，不支援從專案中刪除設備。

刪除設備的唯一方法是刪除包含與設備關聯的 Azure 遷移專案的資源組。

但是，刪除資源組還會刪除與專案關聯的其他已註冊設備、已發現的清單、評估以及資源組中的所有其他 Azure 元件。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>我可以將設備用於不同的訂閱或專案嗎？

使用設備啟動發現後，無法將設備重新配置為與其他 Azure 訂閱一起使用，也不能在其他 Azure 遷移專案中使用它。 您也不能在 vCenter Server 的不同實例上發現 VM。 為這些任務設置新設備。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>是否可以在 Azure VM 上設置設備？

否。 目前，此選項不受支援。 

## <a name="can-i-discover-on-an-esxi-host"></a>我可以在 ESXi 主機上發現嗎？

否。 要發現 VMware VM，您必須具有 vCenter 伺服器。

## <a name="how-do-i-update-the-appliance"></a>如何更新產品？

預設情況下，設備及其安裝的代理會自動更新。 設備每 24 小時檢查一次更新。 將重試失敗的更新。 

這些自動更新僅更新設備和產品代理。 Azure 遷移自動更新不會更新作業系統。 使用 Windows 更新使作業系統保持最新。

## <a name="can-i-check-agent-health"></a>我可以檢查代理運行狀況嗎？

是。 在門戶中，轉到 Azure 遷移的**代理運行狀況**頁：伺服器評估或 Azure 遷移：伺服器遷移工具。 在那裡，可以檢查 Azure 與設備上的發現和評估代理之間的連接狀態。

## <a name="next-steps"></a>後續步驟

閱讀[Azure 遷移概述](migrate-services-overview.md)。
