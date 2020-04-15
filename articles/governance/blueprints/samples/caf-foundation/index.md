---
title: CAF 基礎藍圖範例概觀
description: 適用於 Azure Foundation藍圖範本之雲端採用架構 (CAF) 的概觀和結構
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: a5616667ae909b63f406e399a23bdf568089bf52
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383580"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>適用於 Azure 的 Microsoft 雲端採用架構基礎藍圖範例的概觀

適用於 Azure 的 Microsoft 雲端採用架構 (CAF) 基礎藍圖會部署一組核心基礎結構資源及原則控制項，以用於您第一個生產等級的 Azure 應用程式。 此基礎藍圖是以 CAF 中的建議模式為基礎。

## <a name="architecture"></a>架構

CAF 基礎藍圖範例會在 Azure 中部署建議的基礎結構資源，供組織用來放置管理其雲端資產所需的基礎控制項。 此範例會部署和強制執行資源、原則和範本，讓組織能夠安心地開始使用 Azure。

:::image type="content" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="CAF 基礎，該影像描述 CAF 指引的部分安裝內容，這些內容用於建立 Azure 入門的基礎" border="false":::

此實作會結合數個 Azure 服務，用來提供完全受到監視的企業級安全基礎。 此環境包含：

- [Azure Key Vault](../../../../key-vault/key-vault-overview.md) 執行個體，用來裝載共用服務環境中已部署 VM 所使用的祕密
- 部署 [Log Analytics](../../../../azure-monitor/overview.md)，以確保當您開始對[儲存體帳戶](../../../../storage/common/storage-introduction.md)進行安全部署以取得診斷記錄時，所有動作和服務皆會記錄到中央位置
- 部署 [Azure資訊安全中心](../../../../security-center/security-center-intro.md) (標準版)，為您遷移的工作負載提供威脅防護
- 藍圖也會定義和部署 [Azure 原則](../../../policy/overview.md)，以用於 
  - 套用至資源群組的標記 (CostCenter)
  - 在資源群組中附加具有 CostCenter 標籤的資源
  - 資源和資源群組的允許 Azure 區域
  - 允許的儲存體帳戶 SKU (部署時選擇)
  - 允許的 Azure VM SKU (部署時選擇)
  - 需要部署網路監看式 
  - 需要 Azure 儲存體帳戶的安全傳輸加密
  - 拒絕資源類型 (在部署時選擇)  
- 計畫
  - 啟用 Azure 資訊安全中心的監視功能 (89 項原則)

所有這些項目皆遵循 [Azure 架構中心 - 參考架構](/azure/architecture/reference-architectures/)中所發佈且經過實證的做法。

> [!NOTE]
> CAF 基礎會配置工作負載的基礎架構。
> 在此基礎架構的背後，您仍需要部署工作負載。

如需詳細資訊，請參閱[適用於 Azure 的 Microsoft 雲端採用架構 - 就緒](/azure/cloud-adoption-framework/ready/)。

## <a name="next-steps"></a>後續步驟

您已檢閱 CAF 基礎藍圖範例的概觀和架構。

> [!div class="nextstepaction"]
> [CAF 基礎藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
