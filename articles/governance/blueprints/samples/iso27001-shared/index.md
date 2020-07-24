---
title: ISO 27001 共用服務藍圖範例概觀
description: ISO 27001 共用服務藍圖範例的概觀和架構。 此藍圖範例可協助客戶評定特定 ISO 27001 控制措施。
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 0ef39a4e409320d08529904239dc17fc563c2044
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072873"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 共用服務藍圖範例概觀

ISO 27001 共用服務藍圖範例提供一組符合規範的基礎結構模式和原則防護措施，可協助您取得 ISO 27001 證明。 此藍圖可協助客戶部署雲端式架構，進而將解決方案提供給有認證或合規性需求的案例。

[ISO 27001 App Service 環境/SQL Database 工作負載](../iso27001-ase-sql-workload/index.md)藍圖範例會延續此範例。

## <a name="architecture"></a>架構

ISO 27001 共用服務藍圖範例會在 Azure 中部署基礎結構，讓組織使用此結構來裝載多個以虛擬資料中心 (VDC) 方法為基礎的工作負載。
VDC 是經過實證的一組參考架構、自動化工具和業務開發模型，由 Microsoft 和其最大企業客戶所使用。 共用服務藍圖範例會以完全原生的 Azure VDC 環境為基礎，如下所示。

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="ISO 27001 共用服務藍圖範例設計" border="false":::

此環境包含數個 Azure 服務，用來提供以 ISO 27001 標準為基礎且完全受到監視的企業級安全共用服務基礎結構。 此環境包含：

- [角色型存取控制](../../../../role-based-access-control/overview.md) (RBAC) 角色，以便您從控制層面區分責任。 部署任何基礎結構之前，會定義三個角色：
  - NetOps 角色有權管理網路環境，包括防火牆設定、NSG 設定、路由及其他網路功能
  - SecOps 角色具有部署和管理 [Azure 資訊安全中心](../../../../security-center/security-center-intro.md)和定義 [Azure 原則](../../../policy/overview.md)的必要權限，以及其他與安全性相關的權限
  - SysOps 角色具有在訂用帳戶中定義 [Azure 原則](../../../policy/overview.md)和為整個環境管理 [Log Analytics](../../../../azure-monitor/overview.md) 的必要權限，以及其他與作業相關的權限
- [Log Analytics](../../../../azure-monitor/overview.md) 會部署為第一個 Azure 服務，以確保當您開始進行安全部署時，所有動作和服務皆會記錄到中央位置
- 虛擬網路，其支援用於連接回內部部署資料中心的子網路、用於網際網路連線的輸入和輸出堆疊，以及為進行完整微型分割而使用 NSG 和 ASG 的共用服務子網路，包括：
  - 作為管理用途的 Jumpbox 或防禦主機，其只能透過輸入堆疊子網路中部署的 [Azure 防火牆](../../../../firewall/overview.md)來存取
  - 執行 Active Directory Domain Services (ADDS) 和 DNS 的兩部虛擬機器，這兩部虛擬機器只能透過 Jumpbox 存取，而且可設定為只能透過 VPN 或 [ExpressRoute](../../../../expressroute/expressroute-introduction.md) 連線來複寫 AD (不是由藍圖所部署)
  - 使用 [Azure 網路監看員](../../../../network-watcher/network-watcher-monitoring-overview.md)與標準 DDoS 保護
- [Azure Key Vault](../../../../key-vault/general/overview.md) 執行個體，用來裝載共用服務環境中已部署 VM 所使用的祕密

所有這些項目皆遵循 [Azure 架構中心 - 參考架構](/azure/architecture/reference-architectures/)中所發佈且經過實證的做法。

> [!NOTE]
> ISO 27001 共用服務基礎結構會配置工作負載的基礎架構。
> 在此基礎架構的背後，您仍需要部署工作負載。

如需詳細資訊，請參閱[虛擬資料中心文件](/azure/architecture/vdc/)。

## <a name="next-steps"></a>後續步驟

您已檢閱 ISO 27001 共用服務藍圖範例的概觀和架構。
接下來，請瀏覽下列文章，以深入了解控制項對應及部署此範例的方法：

> [!div class="nextstepaction"]
> [ISO 27001 共用服務藍圖 - 控制項對應](./control-mapping.md)
> [ISO 27001 共用服務藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
