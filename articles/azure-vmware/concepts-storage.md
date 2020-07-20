---
title: 概念-儲存體
description: 瞭解 Azure VMware 解決方案（AVS） Preview 私人雲端中的金鑰儲存體功能。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7f92e8f961422f8354e55192ebdddd077d61acd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84604111"
---
# <a name="azure-vmware-solution-avs-preview-storage-concepts"></a>Azure VMware 解決方案（AVS）預覽儲存體概念

AVS 私用雲端以 VMware vSAN 提供原生、全叢集的儲存體。 叢集中每部主機的所有本機儲存體都會用於 vSAN 資料存放區中，而且預設會提供待用資料加密功能並加以啟用。 您可以使用 Azure 儲存體資源來擴充私人雲端的儲存功能。

## <a name="vsan-clusters"></a>vSAN 叢集

每個叢集主機中的本機儲存體會當做 vSAN 資料存放區的一部分使用。 所有 diskgroups 都會使用 1.6 TB 的 NVMe 快取層，以及原始、每部主機、以 SSD 為基礎的 15.4 TB 容量。 叢集的原始容量層級大小是主機的每個主機容量時間。 例如，四個主機叢集會在 vSAN 容量層中提供 61.6 TB 的原始容量。

叢集主機中的本機儲存體會用於全叢集的 vSAN 資料存放區。 所有資料存放區都會建立為私人雲端部署的一部分，並可立即使用。 Cloudadmin 使用者和 CloudAdmin 群組中的所有使用者都可以使用這些 vSAN 許可權來管理資料存放區：
- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- 資料存放區. DeleteFile
- 資料存放區. FileManagement
- 資料存放區. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>待用資料加密

vSAN 資料存放區預設會使用待用資料加密。 加密解決方案是以 KMS 為基礎，並支援用於金鑰管理的 vCenter 作業。 金鑰會以加密的方式儲存，並以 HSM 為基礎的 Azure Key Vault 主要金鑰包裝。 當主機因任何原因而從叢集中移除時，Ssd 上的資料會立即失效。

## <a name="scaling"></a>調整大小

將主機新增至叢集，即可調整原生叢集儲存容量。 對於使用主機的叢集，原始叢集範圍的容量會隨著每個額外的主機而增加 15.4 TB。 使用 GP 主機建立的叢集，其原始容量會隨著每個額外的主機而增加 7.7 TB。 在這兩種類型的叢集中，主機大約需要10分鐘的時間才能新增至叢集。 如需調整叢集規模的指示，請參閱[調整私用雲端教學][tutorial-scale-private-cloud]課程。

## <a name="azure-storage-integration"></a>Azure 儲存體整合

您可以在私人雲端中執行的工作負載上使用 Azure 儲存體服務。 Azure 儲存體服務包括儲存體帳戶、表格儲存體和 Blob 儲存體。 工作負載與 Azure 儲存體服務的連線不會跨越網際網路。 此連線可提供額外的安全性，並可讓您在私人雲端工作負載中使用以 SLA 為基礎的 Azure 儲存體服務。

## <a name="next-steps"></a>後續步驟

下一步是瞭解私用[雲端身分識別的概念][concepts-identity]。

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
