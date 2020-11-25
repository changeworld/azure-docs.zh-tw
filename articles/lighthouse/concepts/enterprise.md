---
title: Azure Lighthouse 在企業案例中的運用
description: Azure Lighthouse 的功能可在使用多個 Azure AD 租用戶的企業內用來簡化跨租用戶管理。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f452e6810fa6809b5ba1b83b664f8b38d82a895
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004240"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse 在企業案例中的運用

[Azure Lighthouse](../overview.md)的常見案例是在其客戶 Azure Active Directory (Azure AD) 租使用者中管理資源的服務提供者。 不過，Azure Lighthouse 的功能也可以用來簡化企業內使用多個 Azure AD 租使用者的跨租使用者管理。

## <a name="single-vs-multiple-tenants"></a>單一與多個租用戶的比較

對於大部分的組織而言，單一 Azure AD 租用戶的管理比較輕鬆。 讓一個租用戶內的所有資源，都可由指定的使用者、使用者群組，或該租用戶內的服務主體集中進行管理工作。 我們建議盡可能讓貴組織使用一個租用戶。 不過，某些組織可能會有多個 Azure AD 租使用者。 有時候，這可能是暫時性的狀況，就像是在發生收購時，以及尚未定義長期的租使用者匯總策略一樣。 有時候，組織可能需要持續維護多個租使用者，因為完全獨立的子公司、地理或法律需求或其他考慮。

在需要多租使用者架構的情況下，Azure Lighthouse 可以協助集中化和簡化管理作業。 藉由使用 [Azure 委派的資源管理](azure-delegated-resource-management.md)，一個管理租使用者中的使用者可以透過集中、可調整的方式來執行 [跨租使用者管理功能](cross-tenant-management-experience.md) 。

## <a name="tenant-management-architecture"></a>租用戶管理架構

若要在企業中使用 Azure Lighthouse，您必須判斷哪個租使用者將包含在其他租使用者上執行管理作業的使用者。 換句話說，您必須判斷哪個租使用者將是其他租使用者的管理租使用者。

例如，假設您的組織有單一租使用者，我們將會呼叫 *租使用者 a*。然後，您的組織會取得 *租使用者 B* 和 *租使用者 C*，而您的商務原因需要您將其維護為個別的租使用者。

貴組織想要在所有租用戶中使用相同的原則定義、備份做法和安全性程序。 因為租使用者 A 已包含負責這些工作的使用者，您可以將租使用者 B 和租使用者 C 內的訂用帳戶上線，讓租使用者 A 中的相同使用者執行這些工作。

![此圖顯示租使用者 A 中的使用者管理租使用者 B 和租使用者 C 中的資源。](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>安全性和存取考量

在大部分的企業案例中，您會想要將完整訂用帳戶委派給 Azure Lighthouse。 您也可以選擇只委派訂用帳戶內的特定資源群組。

無論何種方式，請務必 [遵循最低許可權原則，定義哪些使用者將可存取委派的資源](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)。 這麼做有助於確保使用者只擁有執行必要工作所需的權限，並可減少意外錯誤的機會。

Azure Lighthouse 只會提供管理租使用者和受管理租使用者之間的邏輯連結，而不是實際移動資料或資源。 此外，存取一律只限單一方向：從管理租用戶到受控租用戶。  管理租用戶中的使用者和群組在對受控租用戶資源執行管理作業時，應繼續使用多重要素驗證。

具有內部或外部治理和合規性防護的企業可以使用 [Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)來符合其透明度需求。 當企業租使用者已建立管理和受管理的租使用者關聯性時，每個租使用者中的使用者都可以查看記錄的活動，以查看使用者在管理租使用者中採取的動作

## <a name="onboarding-considerations"></a>上架考慮

訂用帳戶中的訂用帳戶 (或資源群組) 可透過部署 Azure Resource Manager 範本或透過發行至 Azure Marketplace 的受控服務供應專案，上線至 Azure Lighthouse。

因為企業使用者通常會直接存取企業的租使用者，而不需要行銷或升階管理供應專案，所以部署 Azure Resource Manager 範本通常更快且更簡單。 當上 [架指引](../how-to/onboard-customer.md) 指的是服務提供者和客戶時，企業可以使用相同的程式來讓租使用者上線。

如果您想要，[將受控服務供應項目發佈至 Azure Marketplace](../how-to/publish-managed-services-offers.md)，即可在企業內將租用戶上架。 若要確保供應項目僅適用於適當的租用戶，請確定您的方案已標示為私人。 使用私人方案時，您可以為您打算上架的每個租使用者提供訂用帳戶識別碼，而其他人將無法取得您的供應專案。

## <a name="terminology-notes"></a>術語注意事項

針對企業內的跨租使用者管理，您可以瞭解 Azure Lighthouse 檔中的服務提供者的參考，以套用至企業內的管理租使用者，也就是包含將透過 Azure Lighthouse 管理其他租使用者中資源的使用者的租使用者。 同樣地，您可以將客戶的任何參考都套用至租使用者，而這些租使用者會將資源委派給管理租使用者中的使用者來管理。

例如，在上述範例中，可以將租用戶 A 視為服務提供者租用戶 (管理租用戶)，而租用戶 B 和租用戶 C 則被視為客戶租用戶。

在該範例中，具有適當權限的租用戶 A 使用者可以在 Azure 入口網站的 [我的客戶] 頁面中，[檢視和管理委派的資源](../how-to/view-manage-customers.md)。 同樣地，具有適當權限的租用戶 B 和租用戶 C 使用者可以在 Azure 入口網站的 [服務提供者] 頁面中，[檢視和管理已被委派的資源](../how-to/view-manage-service-providers.md)。

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- 了解 [Azure 委派的資源管理](azure-delegated-resource-management.md)。