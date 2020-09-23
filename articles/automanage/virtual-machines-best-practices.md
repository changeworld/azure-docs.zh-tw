---
title: 適用于虛擬機器的 Azure Automanage 最佳做法
description: 瞭解適用于虛擬機器的 Azure Automanage，其適用于自動為您上線和設定之服務的最佳做法。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: df03fc3049deaf5ce57fda74ca98c748ace1fbbc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933961"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>適用于虛擬機器的 Azure Automanage 最佳做法


當您針對虛擬機器使用 Automanage 時，系統會自動為您上線這些 Azure 服務。 這些是我們的最佳作法白皮書，您可以在我們的 [雲端採用架構](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management)中找到這份白皮書。

針對這些服務，我們將會自動上架、自動設定、監視漂移，以及在偵測到漂移時進行調解。 若要深入瞭解此程式，請參閱 [適用于虛擬機器的 Azure Automanage](automanage-virtual-machines.md)。


## <a name="participating-services"></a>參與的服務

|服務    |描述    |支援的設定檔<sup>1</sup>    |支援的喜好設定<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM 深入解析監視    |適用於 VM 的 Azure 監視器會監視虛擬機器的效能和健康情況，包括其執行中的進程和其他資源的相依性。 深入[瞭解。](../azure-monitor/insights/vminsights-overview.md)    |Azure VM 最佳做法-生產環境    |否    |
|Backup    |Azure 備份提供獨立、隔離的備份，以防止您的 VM 上發生非預期的資料損毀。 深入[瞭解。](../backup/backup-azure-vms-introduction.md) 費用是以受保護 Vm 的數目和大小為基礎。 深入[瞭解。](https://azure.microsoft.com/pricing/details/backup/)    |Azure VM 最佳做法-生產環境    |Yes    |
|Azure 資訊安全中心    |Azure 資訊安全中心是統一的基礎結構安全性管理系統，可強化資料中心的安全性狀態，並為雲端中的混合式工作負載提供先進的威脅防護。 深入[瞭解。](../security-center/security-center-intro.md)  Automanage 會將您的 VM 所在的訂用帳戶設定為 Azure 資訊安全中心的免費層提供。 如果您的訂用帳戶已上線至 Azure 資訊安全中心，automanaged 將不會重新設定。    |Azure VM 最佳作法-生產、Azure VM 最佳作法-開發/測試    |No    |
|Microsoft Antimalware    |適用於 Azure 的 Microsoft Antimalware 是即時保護功能，有助於識別和移除病毒、間諜軟體和其他惡意軟體。 它會在已知的惡意或垃圾軟體嘗試自行安裝或在 Azure 系統上執行時產生警示。 深入[瞭解。](../security/fundamentals/antimalware.md) |Azure VM 最佳作法-生產、Azure VM 最佳作法-開發/測試    |Yes    |
|更新管理    |您可以使用 Azure 自動化中的更新管理來管理虛擬機器的作業系統更新。 您可以快速評估所有代理程式機器上可用更新的狀態，並管理為伺服器安裝必要更新的程序。 深入[瞭解。](../automation/update-management/update-mgmt-overview.md)    |Azure VM 最佳作法-生產、Azure VM 最佳作法-開發/測試    |No    |
|變更追蹤 & 清查    |變更追蹤和清查結合了變更追蹤和清查功能，可讓您追蹤虛擬機器和伺服器基礎結構的變更。 此服務支援在您環境中的服務、守護程式軟體、登錄和檔案之間進行變更追蹤，以協助您診斷不必要的變更並引發警示。 清查支援可讓您查詢客體內的資源，看見已安裝的應用程式和其他設定項目。  深入[瞭解。](../automation/change-tracking.md)    |Azure VM 最佳作法-生產、Azure VM 最佳作法-開發/測試    |No    |
|Azure 自動化帳戶    |Azure 自動化可支援透過基礎結構和應用程式的生命週期進行管理。 深入[瞭解。](../automation/automation-intro.md)    |Azure VM 最佳作法-生產、Azure VM 最佳作法-開發/測試    |No    |
|Log Analytics 工作區    |Azure 監視器會將記錄資料儲存在 Log Analytics 工作區中，也就是 Azure 資源和資料收集、匯總及作為系統管理界限的容器。 深入[瞭解。](../azure-monitor/platform/design-logs-deployment.md)    |Azure VM 最佳作法-生產、Azure VM 最佳作法-開發/測試    |No    |


<sup>1</sup> 當您啟用 Automanage 時可使用的設定檔。 深入[瞭解。](automanage-virtual-machines.md#configuration-profiles) 您也可以調整設定檔的預設設定，並在最佳做法條件約束中設定您自己的喜好設定。


## <a name="next-steps"></a>下一步

請嘗試在 Azure 入口網站中啟用虛擬機器的 Automanage。

> [!div class="nextstepaction"]
> [針對 Azure 入口網站中的虛擬機器啟用 Automanage](quick-create-virtual-machines-portal.md)