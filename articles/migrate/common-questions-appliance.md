---
title: Azure Migrate 設備常見問題
description: 取得 Azure Migrate 設備的常見問題解答。
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 7839c2678152837cc9217e6afe13f7cca36ab4b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630486"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 設備：常見問題

本文將回答有關 Azure Migrate 設備的常見問題。 如果您有其他問題，請查看下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [探索、評量和相依性視覺效果](common-questions-discovery-assessment.md)的相關問題
- [伺服器遷移](common-questions-server-migration.md)的相關問題
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中取得問題的解答

## <a name="what-is-the-azure-migrate-appliance"></a>什麼是 Azure Migrate 設備？

Azure Migrate 設備是一種輕量設備，Azure Migrate：伺服器評估工具用來從內部部署或任何雲端探索及評估實體或虛擬伺服器。 Azure Migrate：伺服器遷移工具也會使用設備來進行內部部署 VMware Vm 的無代理程式遷移。

以下是有關 Azure Migrate 設備的詳細資訊：

- 設備會以 VM 或實體機器的形式部署在內部部署環境中。
- 設備會探索內部部署機器，並持續將電腦中繼資料和效能資料傳送至 Azure Migrate。
- 設備探索無須代理程式。 探索到的機器上未安裝任何內容。

[深入瞭解](migrate-appliance.md) 設備。

## <a name="how-can-i-deploy-the-appliance"></a>如何部署設備？

您可以依照下列方式部署設備：

- 使用範本 ( 探索 VMware Vm。OVA 檔案) 和 Hyper-v Vm (。VHD 檔案) 建立裝載設備的新 VM。
- 如果您不想要使用範本，您可以在現有的實體或虛擬機器上部署設備，以使用 PowerShell 安裝程式腳本（可從入口網站中的 zip 檔案下載）探索 VMware Vm 或 Hyper-v Vm。
- 針對來自內部部署或任何雲端的實體或虛擬伺服器，您一律使用現有伺服器上的腳本部署設備。
- 針對 Azure Government，所有三個設備只能使用 PowerShell 安裝程式腳本進行部署。

## <a name="how-does-the-appliance-connect-to-azure"></a>設備如何連接至 Azure？

設備可透過網際網路或使用 Azure ExpressRoute 進行連線。 請確定已針對設備核准這些 [url](https://docs.microsoft.com/azure/migrate/migrate-appliance#url-access) ，以連接至 Azure。

- 若要將 Azure ExpressRoute 用於 Azure Migrate 複寫流量，必須有 Microsoft 對等互連或現有的公用對等互連 (公用對等互連已針對新的 ER 建立) 淘汰。
- 不支援透過 Azure ExpressRoute (（僅限已啟用) 私人對等互連）進行複寫。

已設定 Microsoft 對等互連的 Azure ExpressRoute 是建議的複寫流量路由網域。

## <a name="does-appliance-analysis-affect-performance"></a>設備分析是否會影響效能？

Azure Migrate 設備會持續分析內部部署機器，以測量效能資料。 這種分析幾乎不會影響分析的機器。

## <a name="can-i-harden-the-appliance-vm"></a>是否可以強化設備 VM？

當您使用下載的範本來建立設備 VM 時，可以將元件新增 (的防毒軟體，例如，如果您離開 Azure Migrate 設備所需的通訊和防火牆規則，則可) 至範本。

## <a name="what-network-connectivity-is-required"></a>需要何種網路連線能力？

設備需要存取 Azure Url。 [檢查](migrate-appliance.md#url-access) URL 清單。

## <a name="what-data-does-the-appliance-collect"></a>設備會收集哪些資料？

請參閱下列文章，以取得 Azure Migrate 設備在 Vm 上收集之資料的相關資訊：

- **VMWARE VM**： [檢查](migrate-appliance.md#collected-data---vmware) 收集的資料。
- **HYPER-V VM**： [檢查](migrate-appliance.md#collected-data---hyper-v) 收集的資料。
- **實體或虛擬伺服器**：[檢查](migrate-appliance.md#collected-data---physical) 收集的資料。

## <a name="how-is-data-stored"></a>儲存資料的方式

Azure Migrate 設備所收集的資料會儲存在您建立 Azure Migrate 專案所在的 Azure 位置。

以下是有關資料儲存方式的詳細資訊：

- 收集的資料會安全地儲存在 Microsoft 訂用帳戶的 CosmosDB 中。 當您刪除 Azure Migrate 專案時，就會刪除資料。 儲存體是由 Azure Migrate 處理。 您無法特別針對收集的資料選擇儲存體帳戶。
- 如果您使用相依性 [視覺效果](concepts-dependency-visualization.md)，所收集的資料會儲存在 azure 訂用帳戶中建立的 Azure Log Analytics 工作區中。 當您刪除訂用帳戶中的 Log Analytics 工作區時，會刪除資料。 

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>連續分析期間有多少資料上傳？

傳送給 Azure Migrate 的資料量取決於多個參數。 例如，具有10部機器的 Azure Migrate 專案 (各有一個磁片和一個 NIC) 每天傳送大約 50 MB 的資料。 此值為近似值;實際值會根據磁片和 Nic 的資料點數目而有所不同。 如果電腦、磁片或 Nic 的數目增加，則傳送的資料增加是非線性的。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>待用資料和傳輸中的資料是否會加密？

是，適用于兩者：

- 中繼資料會透過 HTTPS 透過網際網路安全地傳送到 Azure Migrate 服務。
- 中繼資料會儲存在 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 資料庫和 [azure Blob 儲存體](../storage/common/storage-service-encryption.md) 的 Microsoft 訂用帳戶中。 中繼資料會針對儲存體進行待用加密。
- 相依性分析的資料也會在安全 HTTPS)  (傳輸中加密。 它會儲存在您訂用帳戶的 Log Analytics 工作區中。 資料會針對相依性分析進行待用加密。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>設備如何連接到 vCenter Server？

這些步驟描述設備如何連線至 VMware vCenter Server：

1. 設備會使用您在設定設備時所提供的認證，連接到 vCenter Server (埠 443) 。
2. 設備會使用 VMware PowerCLI 來查詢 vCenter Server，以收集由 vCenter Server 管理之 Vm 的相關中繼資料。
3. 設備會收集有關 Vm (核心、記憶體、磁片、Nic) 的設定資料，以及每個 VM 在過去一個月的效能歷程記錄。
4. 收集的中繼資料會透過網際網路透過 HTTPS)  (傳送至 Azure Migrate：伺服器評定工具，以進行評量。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate 設備是否可以連接到多部 vCenter Server？

否。 [Azure Migrate 設備](migrate-appliance.md)與 vCenter Server 之間有一對一的對應。 若要在多個 vCenter Server 實例上探索 Vm，您必須部署多個設備。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure Migrate 專案可以有多個設備嗎？

專案可以附加多個設備。 不過，設備只能與一個專案相關聯。 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate 設備/複寫設備是否可以連接到相同的 vCenter？

是。 您可以新增 Azure Migrate 設備 (用於評量和無代理程式 VMware 遷移) ，而複寫設備 (用於將 VMware Vm 以代理程式為基礎的遷移) 至相同的 vCenter server。 但請確定您未在相同的 VM 上設定這兩個設備，且目前不受支援。

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>我可以使用設備探索多少部 Vm 或伺服器？

您最多可以探索10000個 VMware Vm，最多可達5000部 Hyper-v Vm，最多可達1000部具有單一設備的實體伺服器。 如果您的內部部署環境中有更多電腦，請閱讀 [調整 hyper-v 評定](scale-hyper-v-assessment.md)、 [調整 VMware 評定](scale-vmware-assessment.md)，以及 [調整實體伺服器評](scale-physical-assessment.md)量的相關資訊。

## <a name="can-i-delete-an-appliance"></a>我可以刪除設備嗎？

目前不支援從專案刪除設備。

刪除設備的唯一方法是刪除資源群組，其中包含與設備相關聯的 Azure Migrate 專案。

不過，刪除資源群組也會刪除其他已註冊的設備、已探索的清查、評量，以及資源群組中與專案相關聯的所有其他 Azure 元件。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>我可以使用設備搭配不同的訂用帳戶或專案嗎？

若要搭配不同的訂用帳戶或專案使用設備，您必須針對設備機器上 (VMware/Hyper-v/實體) 的特定案例執行 PowerShell 安裝程式腳本，以重新設定現有的設備。 腳本將會清除現有的設備元件和設定，以部署全新的設備。 開始使用新部署的設備設定管理員之前，請務必清除瀏覽器快取。

此外，您無法在重新設定的設備上重複使用現有的 Azure Migrate 專案金鑰。 請務必從所需的訂用帳戶/專案產生新的金鑰，以完成設備註冊。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>我可以在 Azure VM 上設定設備嗎？

否。 目前不支援此選項。 

## <a name="can-i-discover-on-an-esxi-host"></a>我可以在 ESXi 主機上探索嗎？

否。 若要探索 VMware Vm，您必須有 vCenter Server。

## <a name="how-do-i-update-the-appliance"></a>如何? 更新設備？

預設會自動更新設備及其已安裝的代理程式。 設備每隔24小時會檢查一次更新。 失敗的更新會重試。 

這些自動更新只會更新設備和設備代理程式。 Azure Migrate 自動更新不會更新作業系統。 使用 Windows 更新讓作業系統保持在最新狀態。

## <a name="can-i-check-agent-health"></a>我可以檢查代理程式健康情況嗎？

是。 在入口網站中，移至 [Azure Migrate：伺服器評定] 或 [Azure Migrate：伺服器遷移工具] 的 [ **代理程式健康** 情況] 頁面。 在那裡，您可以檢查 Azure 與設備上的探索和評量代理程式之間的線上狀態。

## <a name="next-steps"></a>後續步驟

閱讀 [Azure Migrate 總覽](migrate-services-overview.md)。
