---
title: Azure Migrate 設備的相關常見問題
description: 取得 Azure Migrate 設備的常見問題解答
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 3bb066b08447a951665e629da5ebcb75714b9f1e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425350"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 設備：常見的問題

本文會回答有關 Azure Migrate 設備的常見問題。 如果您有其他問題，請參閱下列文章：

- 有關 Azure Migrate 的[一般問題](resources-faq.md)。
- 探索、評估和相依性視覺效果的相關[問題](common-questions-discovery-assessment.md)。
- 伺服器遷移的相關[問題](common-questions-server-migration.md)。
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)上獲得解答。 


## <a name="what-is-the-azure-migrate-appliance"></a>什麼是 Azure Migrate 設備？

Azure Migrate 設備是一種輕量設備，由 Azure Migrate：伺服器評估工具用來探索及評估內部部署伺服器。 Azure Migrate：伺服器遷移工具也會使用此設備來進行內部部署 VMware Vm 的無代理程式遷移。 

- 應用裝置會部署在內部部署環境中做為 VM 或實體機器。
- 設備會探索內部部署機器，並持續將電腦中繼資料和效能資料傳送至 Azure Migrate。
- 設備探索無須代理程式。 探索到的機器上未安裝任何內容。

[深入瞭解](migrate-appliance.md)設備。

## <a name="how-does-the-appliance-connect-to-azure"></a>設備如何連接到 Azure？

設備可以透過網際網路連接，或使用 Azure ExpressRoute 搭配公用/Microsoft 對等互連。

## <a name="does-appliance-analysis-impact-performance"></a>設備分析是否會影響效能？

Azure Migrate 設備會持續分析內部部署機器，以測量效能資料。 此分析幾乎不會影響分析機器的效能。

### <a name="can-i-harden-the-appliance-vm"></a>我可以強化設備 VM 嗎？

當您使用已下載的範本建立設備 VM 時，只要您保留 Azure Migrate 設備所需的通訊和防火牆規則，就可以將元件（例如防毒軟體）新增至範本。


## <a name="what-network-connectivity-is-needed"></a>需要何種網路連線？

請參閱網路連線能力的相關資訊：
- 使用 Azure Migrate 設備的 VMware 評估： [URL](migrate-appliance.md#url-access)和[埠](migrate-support-matrix-vmware.md#port-access)存取需求。
- 使用 Azure Migrate 設備的 VMware 無代理程式遷移： [URL](migrate-appliance.md#url-access)和[埠](migrate-support-matrix-vmware-migration.md#agentless-ports)存取需求。
- 使用 Azure Migrate 設備的 hyper-v 評估： [URL](migrate-appliance.md#url-access)和[埠](migrate-support-matrix-hyper-v.md#port-access)存取需求。


## <a name="what-data-does-the-appliance-collect"></a>設備會收集哪些資料？

檢查收集的資料：

- VMware VM[效能資料](migrate-appliance.md#collected-performance-data-vmware)和[元](migrate-appliance.md#collected-metadata-vmware)資料。
- Hyper-v VM[效能資料](migrate-appliance.md#collected-performance-data-hyper-v)和[元](migrate-appliance.md#collected-metadata-hyper-v)資料。


## <a name="how-is-data-stored"></a>儲存資料的方式

Azure Migrate 應用裝置所收集的資料會儲存在您建立 Azure Migrate 專案所在的 Azure 位置。 

- 資料會安全地儲存在 Microsoft 訂用帳戶中，並在您刪除 Azure Migrate 專案時刪除。
- 如果您使用相依性[視覺效果](concepts-dependency-visualization.md)，收集的資料會儲存在美國，在 Azure 訂用帳戶中建立的 Log Analytics 工作區中。 刪除訂用帳戶中的 Log Analytics 工作區時，會刪除此資料。

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>連續分析中有多少資料已上傳？

傳送至 Azure Migrate 的資料量取決於多個參數。 為了讓您有意義，有10部機器的 Azure Migrate 專案（每個都有一個磁片和一個 NIC）每天大約會傳送 50 MB。 這個值是根據 Nic 和磁片的資料點數目而定的近似值和 changes。 如果電腦、Nic 或磁片數目增加，則傳送的資料增加為非線性。

## <a name="is-data-encrypted-at-restin-transit"></a>資料是否會在待用/傳輸中加密？

是，適用于兩者。

- 中繼資料會透過 HTTPS 安全地透過網際網路傳送至 Azure Migrate 服務。
- 中繼資料會儲存在[Azure Cosmos](../cosmos-db/database-encryption-at-rest.md)資料庫和[azure Blob 儲存體](../storage/common/storage-service-encryption.md)的 Microsoft 訂用帳戶中。 中繼資料會進行待用加密。
- 相依性分析的資料也會在傳輸中加密（安全 HTTPS）。 它會儲存在您訂用帳戶的 Log Analytics 工作區中。 它也會進行待用加密。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>設備如何連接到 vCenter Server？

1. 設備會使用您在設定設備時提供的認證，連接到 vCenter Server （埠443）。
2. 設備會使用 VMware PowerCLI 來查詢 vCenter Server，以收集 vCenter Server 所管理之 Vm 的相關中繼資料。
3. 設備會收集關於 Vm （核心、記憶體、磁片、Nic）的設定資料，以及每個 VM 在過去一個月的效能歷程記錄。
4. 收集的中繼資料會傳送至 Azure Migrate：伺服器評估（透過網際網路透過 HTTPS）以進行評量。

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>我可以將設備連線到多部 vCenter Server 嗎？

否。 設備與 vCenter Server 之間會有一對一的對應。 若要探索多個 vCenter Server 實例上的 Vm，您必須部署多個應用裝置。

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>我可以使用設備探索多少部 Vm 或伺服器？

您最多可以探索10000個 VMware Vm、最多5000部 Hyper-v Vm，以及最多250個具有單一應用裝置的實體伺服器。 如果您的內部部署環境中有更多電腦，請參閱調整[hyper-v](scale-hyper-v-assessment.md)、 [VMware](scale-vmware-assessment.md)和[實體](scale-physical-assessment.md)評量。

## <a name="can-i-delete-an-appliance"></a>我可以刪除設備嗎？

目前不支援從專案中刪除應用裝置。

- 若要刪除設備，唯一的方法是刪除資源群組，其中包含與設備相關聯的 Azure Migrate 專案。
- 不過，刪除資源群組也會刪除其他已註冊的設備、探索到的清查、評量，以及與資源群組中的專案相關聯的所有其他 Azure 元件。


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>我可以使用具有不同訂用帳戶/專案的設備嗎？

使用設備啟動探索之後，您無法使用不同的 Azure 訂用帳戶重新設定它，或在不同的 Azure Migrate 專案中使用它。 您也無法在不同的 vCenter Server 上探索 Vm。 為這些工作設定新的設備。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>我可以在 Azure VM 上設定設備嗎？
否。 目前不支援這種情況。 

## <a name="can-i-discover-on-an-esxi-host"></a>我可以在 ESXi 主機上探索嗎？
否。 您需要 vCenter Server 來探索 VMware Vm。

## <a name="how-do-i-update-the-appliance"></a>如何? 更新設備嗎？

根據預設，應用裝置及其安裝的代理程式會自動更新。 設備會每隔24小時檢查一次更新。 如果更新程式失敗，則會重試。 自動更新只會更新設備和應用裝置代理程式。 作業系統未更新。 使用 Microsoft Updates 讓作業系統保持在最新狀態。

## <a name="can-i-check-agent-health"></a>可以檢查代理程式健康情況嗎？

在入口網站中，移至伺服器評估或伺服器遷移工具中的 [**代理程式健康**情況] 頁面。 在這裡，您可以檢查設備和 Azure 上的探索和評估代理程式之間的線上狀態。

## <a name="next-steps"></a>後續步驟
閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
