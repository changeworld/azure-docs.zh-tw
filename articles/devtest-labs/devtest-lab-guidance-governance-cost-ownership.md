---
title: 管理 Azure DevTest Labs 中的成本與擁有權
description: 本文提供的資訊可協助您針對成本進行優化，並在您的環境中調整擁有權。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dbf0bb9cae87a51eb6e0a4fb25c700ec6f423ff1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476252"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>治理 Azure DevTest Labs 基礎結構 - 管理成本和擁有權
當您考慮建置開發和測試環境時，成本和擁有權是主要考量。 在此節中，您會發現可協助您進行成本最佳化，並在環境中配置擁有權的資訊。

## <a name="optimize-for-cost"></a>進行成本最佳化

### <a name="question"></a>問題
如何在 DevTest Labs 環境內進行成本最佳化？

### <a name="answer"></a>回答
有多個 DevTest Labs 的內建功能，可協助您進行成本最佳化。 請參閱[成本管理、閾值](devtest-lab-configure-cost-management.md) [及原則](devtest-lab-set-lab-policy.md)文章，以限制使用者的活動。 

當您針對開發和測試工作負載運用 DevTest Labs 時，您可以考慮使用 [Enterprise 開發/測試訂用帳戶權益](https://azure.microsoft.com/offers/ms-azr-0148p/)作為 Enterprise 合約的一部分。 或者，如果您是隨用隨付的客戶，您可能要考慮[隨用隨付開發/測試供應項目](https://azure.microsoft.com/offers/ms-azr-0023p/)。

這種方法可為您提供許多優點：

- 享有 Windows 虛擬機器、雲端服務、HDInsight、App Service 及 Logic Apps 的更優惠開發/測試費率
- 在其他 Azure 服務上享有絕佳的 Enterprise 合約 (EA) 費率
- 可以存取資源庫中的專屬開發/測試映像，包括 Windows 8.1 和 Windows 10
 
僅有效的 Visual Studio 訂閱者 (標準訂用帳戶、年度雲端訂用帳戶及每月雲端訂用帳戶) 能夠使用在 Enterprise 開發/測試訂用帳戶內執行的 Azure 資源。 不過，終端使用者能夠存取應用程式來提供意見反應或執行接受度測試。 此訂用帳戶內的資源僅限用於應用程式的開發與測試，且不附帶正常運作的保證。

如果您決定使用 DevTest 供應項目，請注意，此項權益專門用於應用程式的開發與測試。 訂用帳戶內的使用量不包含財務支援的 SLA，除了使用 Azure DevOps 及 HockeyApp 之外。

## <a name="define-a-role-based-access-across-your-organization"></a>定義您整個組織內的角色型存取
### <a name="question"></a>問題
如何為我的 DevTest Labs 環境定義角色型存取控制，以確保 IT 可以治理，而開發人員/測試同時還能執行其工作？ 

### <a name="answer"></a>回答
有一個廣泛的模式，但詳細資料取決於您的組織。

中央 IT 應該只擁有必要項目，並讓專案和應用程式小組能夠擁有所需的控制層級。 一般而言，這表示中央 IT 擁有訂用帳戶並會處理核心 IT 函式，例如網路設定。 這組適用於訂用帳戶的**擁有者**應該很小。 這些擁有者可以在需要時提名其他擁有者，或套用訂用帳戶層級的原則，例如「沒有公用 IP」。

可能有使用者子集需要跨訂用帳戶進行存取，例如第 1 層或第 2 層支援。 在此情況下，我們建議您為這些使用者授與**參與者**存取權，讓他們可以管理資源，但不提供使用者存取權或調整原則。

DevTest Labs 資源應該由接近專案/應用程式小組的擁有者所擁有。 這是因為他們了解對於機器及必要軟體的需求。 在大多數組織中，此 DevTest Labs 資源的擁有者通常是專案/開發潛在客戶。 此擁有者可以管理實驗室環境內的使用者和原則，而且可以管理 DevTest Labs 環境中的所有 VM。

您應該將專案/應用程式小組成員新增至 DevTest Labs 使用者角色。 這些使用者可以建立虛擬機器 (使用實驗室和訂用帳戶層級原則來內建)。 他們也可以管理自己的虛擬機器。 他們無法管理屬於其他使用者的虛擬機器。

如需詳細資訊，請參閱[Azure 企業 scaffold-規定的訂](/azure/architecture/cloud-adoption/appendix/azure-scaffold)用帳戶治理檔。


## <a name="next-steps"></a>後續步驟
請參閱[公司原則與合規性](devtest-lab-guidance-governance-policy-compliance.md)。
