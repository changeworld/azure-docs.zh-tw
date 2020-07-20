---
title: Azure Migrate 設備常見問題
description: 取得 Azure Migrate 設備的常見問題解答。
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: afdede99fa4485ba7e5494e844d795c2d5d3deee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078980"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 設備：常見的問題

本文會回答有關 Azure Migrate 設備的常見問題。 如果您有其他問題，請檢查下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [探索、評估和相依性視覺效果](common-questions-discovery-assessment.md)的相關問題
- [伺服器遷移](common-questions-server-migration.md)的相關問題
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中獲得解答

## <a name="what-is-the-azure-migrate-appliance"></a>什麼是 Azure Migrate 設備？

Azure Migrate 設備是一種輕量設備，Azure Migrate：伺服器評估工具用來探索及評估內部部署伺服器。 Azure Migrate：伺服器遷移工具也會使用設備來進行內部部署 VMware Vm 的無代理程式遷移。

以下是 Azure Migrate 設備的詳細資訊：

- 應用裝置會部署在內部部署環境中做為 VM 或實體機器。
- 設備會探索內部部署機器，並持續將電腦中繼資料和效能資料傳送至 Azure Migrate。
- 設備探索無須代理程式。 探索到的機器上未安裝任何內容。

[深入瞭解](migrate-appliance.md)設備。

## <a name="how-can-i-deploy-the-appliance"></a>如何部署設備？

應用裝置可以部署如下：

- 使用適用于 VMware Vm 和 Hyper-v Vm （適用于 VMware 的 OVA 範本或適用于 Hyper-v 的 VHD）的範本。
- 如果您不想要使用範本，或您處於 Azure Government 中，您可以使用 PowerShell 腳本來部署適用于 VMware 或 Hyper-v 的應用裝置。
- 針對實體伺服器，您一律會使用指令碼來部署設備。


## <a name="how-does-the-appliance-connect-to-azure"></a>設備如何連接到 Azure？

設備可以透過網際網路連線，或使用 Azure ExpressRoute 搭配公用/Microsoft 對等互連來連接。

## <a name="does-appliance-analysis-affect-performance"></a>設備分析是否會影響效能？

Azure Migrate 設備會持續分析內部部署機器，以測量效能資料。 此分析幾乎不會對已分析的機器造成效能影響。

## <a name="can-i-harden-the-appliance-vm"></a>我可以強化設備 VM 嗎？

當您使用已下載的範本來建立設備 VM 時，如果您保留 Azure Migrate 設備所需的通訊和防火牆規則，您可以將元件（例如，防毒軟體）新增至範本。

## <a name="what-network-connectivity-is-required"></a>需要何種網路連線？


設備需要存取 Azure Url。 [檢查](migrate-appliance.md#url-access)URL 清單。

## <a name="what-data-does-the-appliance-collect"></a>設備會收集哪些資料？

如需 Azure Migrate 設備在 Vm 上收集之資料的相關資訊，請參閱下列文章：

- **VMWARE VM**：[檢查](migrate-appliance.md#collected-data---vmware)收集的資料。 [
- **HYPER-V VM**：[檢查](migrate-appliance.md#collected-data---hyper-v)收集的資料。

## <a name="how-is-data-stored"></a>儲存資料的方式

Azure Migrate 應用裝置所收集的資料會儲存在您建立 Azure Migrate 專案所在的 Azure 位置。

以下是資料儲存方式的詳細資訊：

- 收集的資料會安全地儲存在 Microsoft 訂用帳戶的 CosmosDB 中。 當您刪除 Azure Migrate 專案時，就會刪除資料。 儲存體是由 Azure Migrate 處理。 您無法特別針對收集的資料選擇儲存體帳戶。
- 如果您使用相依性[視覺效果](concepts-dependency-visualization.md)，所收集的資料會儲存在美國 Azure Log Analytics 工作區中建立的 azure 訂用帳戶中。 當您刪除訂用帳戶中的 Log Analytics 工作區時，會刪除資料。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>連續分析期間會上傳多少資料？

傳送至 Azure Migrate 的資料量取決於多個參數。 例如，具有10部電腦的 Azure Migrate 專案（每個都有一個磁片和一個 NIC）每天會傳送大約 50 MB 的資料。 這個值是近似值;實際的值會根據磁片和 Nic 的資料點數目而有所不同。 如果電腦、磁片或 Nic 數目增加，則傳送的資料量會增加非線性。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>資料是否會在待用和傳輸中加密？

是，適用于兩者：

- 中繼資料會透過 HTTPS 安全地透過網際網路傳送至 Azure Migrate 服務。
- 中繼資料會儲存在[Azure Cosmos](../cosmos-db/database-encryption-at-rest.md)資料庫和 Microsoft 訂用帳戶的[azure Blob 儲存體](../storage/common/storage-service-encryption.md)中。 中繼資料會在待用時加密以供儲存之用。
- 相依性分析的資料也會在傳輸中加密（透過安全的 HTTPS）。 它會儲存在您訂用帳戶的 Log Analytics 工作區中。 資料會在待用時加密，以進行相依性分析。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>設備如何連接到 vCenter Server？

這些步驟說明設備如何連接到 VMware vCenter Server：

1. 設備會使用您在設定設備時所提供的認證，連接到 vCenter Server （埠443）。
2. 設備會使用 VMware PowerCLI 來查詢 vCenter Server，以收集 vCenter Server 所管理之 Vm 的相關中繼資料。
3. 設備會收集關於 Vm （核心、記憶體、磁片、Nic）的設定資料，以及每個 VM 在過去一個月的效能歷程記錄。
4. 收集的中繼資料會傳送至 Azure Migrate：伺服器評估工具（透過網際網路透過 HTTPS）以進行評量。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate 設備是否可以連接到多部 vCenter Server？

否。 [Azure Migrate 設備](migrate-appliance.md)與 vCenter Server 之間有一對一的對應。 若要探索多個 vCenter Server 實例上的 Vm，您必須部署多個應用裝置。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure Migrate 專案可以有多個設備嗎？
一個專案可以附加多個設備。 不過，應用裝置只能與一個專案相關聯。 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate 的應用裝置/複寫設備是否可以連接到相同的 vCenter？
是。 您可以將 Azure Migrate 設備（用於評估和無代理程式 VMware 遷移）和複寫設備（用於 VMware Vm 的代理程式型遷移）新增至相同的 vCenter server。


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>我可以使用設備探索多少部 Vm 或伺服器？

您最多可以探索10000個 VMware Vm、最多5000部 Hyper-v Vm，以及最多1000個具有單一應用裝置的實體伺服器。 如果您的內部部署環境中有更多電腦，請閱讀[調整 hyper-v 評估](scale-hyper-v-assessment.md)、[調整 VMware 評估](scale-vmware-assessment.md)和[調整實體伺服器評](scale-physical-assessment.md)量。

## <a name="can-i-delete-an-appliance"></a>我可以刪除設備嗎？

目前不支援從專案中刪除應用裝置。

若要刪除設備，唯一的方法是刪除資源群組，其中包含與設備相關聯的 Azure Migrate 專案。

不過，刪除資源群組也會刪除其他已註冊的設備、已探索的清查、評量，以及資源群組中與專案相關聯的所有其他 Azure 元件。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>我可以使用具有不同訂用帳戶或專案的設備嗎？

使用設備來起始探索之後，您就無法重新設定設備以與不同的 Azure 訂用帳戶搭配使用，而且您無法在不同的 Azure Migrate 專案中使用它。 您也無法在不同的 vCenter Server 實例上探索 Vm。 為這些工作設定新的設備。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>我可以在 Azure VM 上設定設備嗎？

否。 目前不支援此選項。 

## <a name="can-i-discover-on-an-esxi-host"></a>我可以在 ESXi 主機上探索嗎？

否。 若要探索 VMware Vm，您必須有 vCenter Server。

## <a name="how-do-i-update-the-appliance"></a>如何? 更新設備嗎？

根據預設，應用裝置及其安裝的代理程式會自動更新。 設備會每24小時檢查一次更新。 失敗的更新會重試。 

這些自動更新只會更新設備和應用裝置代理程式。 Azure Migrate 自動更新不會更新作業系統。 使用 Windows Update 讓作業系統保持在最新狀態。

## <a name="can-i-check-agent-health"></a>可以檢查代理程式健康情況嗎？

是。 在入口網站中，移至 [Azure Migrate：伺服器評定] 或 [Azure Migrate：伺服器遷移工具] 的 [**代理程式健康**情況] 頁面。 在這裡，您可以檢查 Azure 與設備上的探索與評估代理程式之間的線上狀態。

## <a name="next-steps"></a>後續步驟

閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
