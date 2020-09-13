---
title: 藍圖範例索引
description: 使用 Azure 藍圖部署環境、原則和雲端採用架構基礎的合規性和標準範例索引。
ms.date: 09/11/2020
ms.topic: sample
ms.openlocfilehash: 7ac95dee88cb244ecb4b60738daae26bb0d8831a
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031869"
---
# <a name="azure-blueprints-samples"></a>Azure 藍圖範例

下表包含 Azure 藍圖的範例連結。 每個範例均具備生產品質且可立即部署，協助您達成各種合規性需求。

## <a name="standards-based-blueprint-samples"></a>標準型藍圖範例

| 範例 | 描述 |
|---------|---------|
| [澳大利亞政府受保護的 ISM](./ism-protected/index.md) | 針對澳大利亞政府受保護的 ISM 合規性提供防護措施。 |
| [Azure 安全性效能評定](./azure-security-benchmark.md) | 提供符合 [Azure 安全性效能評定](../../../security/benchmarks/overview.md)的防護措施。 |
| [加拿大聯邦 PBMM](./canada-federal-pbmm/index.md) | 提供符合加拿大聯邦受保護分類 B、中度完整性、中度可用性之保護機制 (PBMM)。 |
| [CIS Microsoft Azure 基礎基準測試](./cis-azure-1-1-0.md)| 提供一組原則，以協助您遵循 CIS Microsoft Azure 基礎的基準測試建議。 |
| [DoD 影響等級 4](./dod-impact-level-4/index.md) | 提供一組原則，以協助符合 DoD 影響等級 4 的規範。 |
| [DoD 影響等級 5](./dod-impact-level-5/index.md) | 提供一組原則，以協助符合 DoD 影響等級 5 的規範。 |
| [FedRAMP 適中等級](./fedramp-m/index.md) | 提供一組原則，以協助符合 FedRAMP 適中等級的規範。 |
| [FedRAMP 高等級](./fedramp-h/index.md) | 提供一組原則，以協助符合 FedRAMP 高等級的規範。 |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | 提供一組原則，以協助符合 HIPAA HITRUST。 |
| [IRS 1075](./irs-1075/index.md) | 提供成立條件以符合 IRS 1075。|
| [ISO 27001](./iso27001/index.md) | 提供成立條件以符合 ISO 27001。 |
| [ISO 27001 共用服務](./iso27001-shared/index.md) | 提供一組符合規範的基礎結構模式和原則防護措施，可協助您取得 ISO 27001 證明。 |
| [ISO 27001 App Service 環境/SQL Database 工作負載](./iso27001-ase-sql-workload/index.md) | 提供 [ISO 27001 共用服務](./iso27001-shared/index.md)藍圖範例的其他基礎結構。 |
| [媒體](./media/index.md) | 提供一組原則，以協助符合 Media MPAA。 |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | 提供成立條件以符合 NIST SP 800-53 R4。 |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | 提供成立條件以符合 NIST SP 800-171 R2。 |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | 提供一組原則以協助符合 PCI-DSS v3.2.1 規範。 |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | 協助達成 SWIFT CSP-CSCF v2020 中的合規性。 |
| [UK OFFICIAL 與 UK NHS Governance](./ukofficial/index.md) | 提供一組符合規範的基礎結構模式和原則防護措施，可協助您取得 UK OFFICIAL 與 UK NHS 證明。 |
| [CAF 基礎](./caf-foundation/index.md) | 提供一組控制項來協助您管理雲端資產，使其與[適用於 Azure 的 Microsoft 雲端採用架構 (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index)一致。 |
| [CAF 移轉登陸區域](./caf-migrate-landing-zone/index.md) | 提供一組控制項來協助您設定第一個工作負載的移轉及管理雲端資產，使其與[適用於 Azure 的 Microsoft Cloud 採用架構 (CAF)](/azure/architecture/cloud-adoption/migrate/index) 一致。 |

## <a name="samples-strategy"></a>範例策略

:::image type="content" source="../media/blueprint-samples-strategy.png" alt-text="藍圖範例策略" border="false":::

CAF 基礎和 CAF 移轉登陸區域藍圖會假設客戶正在準備現有的全新單一訂用帳戶，以便將內部部署資產及工作負載遷移至 Azure。
(圖中的區域 A 和 B)。  

您有機會逐一查看範例藍圖，並尋找客戶所套用的自訂模式。 也有機會主動處理產業專屬的藍圖，像是金融服務和電子商務 (區域 B 的頂端)。 同樣地，我們已預想到要為複雜的架構考量建立藍圖，例如多個訂用帳戶、高可用性、跨區域資源，以及對現有訂用帳戶和資源實作控制項的客戶 (區域 C 和 D)。

有一些範例藍圖可用於合規性需求及架構複雜性都很高的客戶案例 (圖中的區域 E)。 圖中的區域 F 就是由客戶和合作夥伴處理的區域，他們運用範例藍圖並根據其獨特需求進行自訂。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。
