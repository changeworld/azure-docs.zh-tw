---
title: 概念-儲存體
description: 深入瞭解 Azure VMware Solution 私用雲端中的重要儲存體功能。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 02378040061080d3c9abbfafb26180c9d22e9073
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316812"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 解決方案儲存體概念

Azure VMware 解決方案私用雲端提供原生的全叢集儲存體與 VMware vSAN。 叢集中每個主機的所有本機儲存體都會使用於 vSAN 資料存放區中，而且預設會提供待用資料加密功能。 您可以使用 Azure 儲存體資源來擴充私人雲端的儲存功能。

## <a name="vsan-clusters"></a>vSAN 叢集

每個叢集主機中的本機儲存體會用來作為 vSAN 資料存放區的一部分。 所有 diskgroups 都會使用 NVMe 快取層（1.6 TB）搭配原始、每部主機、以 SSD 為基礎的 15.4 TB 容量。 叢集的原始容量層大小是主機的每個主機容量時間。 例如，四個主機叢集將會在 vSAN 容量層中提供 61.6-TB 的原始容量。

叢集主機中的本機儲存體會用於整個叢集的 vSAN 資料存放區。 所有資料存放區都會建立為私用雲端部署的一部分，而且可立即使用。 Cloudadmin 使用者和 CloudAdmin 群組中的所有使用者都可以使用這些 vSAN 許可權來管理資料存放區：
- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- 資料存放區. DeleteFile
- 資料存放區. FileManagement
- 資料存放區. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>待用資料加密

vSAN 資料存放區預設使用待用資料加密。 加密解決方案是以 KMS 為基礎，而且支援進行金鑰管理的 vCenter 作業。 金鑰會以 HSM 為基礎的 Azure Key Vault 主要金鑰來加密儲存。 當主機因為任何原因而從叢集移除時，Ssd 上的資料會立即失效。

## <a name="scaling"></a>調整大小

藉由將主機新增至叢集來調整原生叢集儲存體容量。 針對使用主機的叢集，每個額外的主機都會增加 15.4 TB 的原始叢集容量。 以 GP 主機建立的叢集，每個額外的主機都會有 7.7 TB 的原始容量增加。 在這兩種類型的叢集中，主機大約需要10分鐘的時間才會新增至叢集。 請參閱「 [調整私用雲端」教學][tutorial-scale-private-cloud] 課程，以取得調整叢集的指示。

## <a name="azure-storage-integration"></a>Azure 儲存體整合

您可以在私人雲端中執行的工作負載上使用 Azure 儲存體服務。 Azure 儲存體服務包括儲存體帳戶、資料表儲存體和 Blob 儲存體。 工作負載與 Azure 儲存體服務的連接不會跨越網際網路。 此連線可提供額外的安全性，並可讓您在私人雲端工作負載中使用以 SLA 為基礎的 Azure 儲存體服務。

## <a name="next-steps"></a>後續步驟

下一步是瞭解私用 [雲端身分識別概念][concepts-identity]。

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
