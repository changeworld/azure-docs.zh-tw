---
title: ISV 案例中的 Azure Lighthouse
description: Isv 可以使用 Azure Lighthouse 的功能，以提供客戶供應專案更多的彈性。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696337"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV 案例中的 Azure Lighthouse

[Azure Lighthouse](../overview.md)的常見案例是在其客戶 Azure Active Directory (Azure AD) 租使用者中管理資源的服務提供者。 Azure Lighthouse 的功能也可供獨立軟體廠商 (Isv) 搭配其客戶使用 SaaS 型供應專案。 對於提供需要存取訂用帳戶範圍之受控服務或支援的 Isv，Azure Lighthouse 可能特別有用。

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace 中的受控服務供應項目

如果您是 ISV，您可能已經發佈 Azure Marketplace 的解決方案。 如果您提供受控服務給客戶，您可以發佈受控服務供應專案來達到此目的。 這些優惠可簡化上執行緒序，並讓您的服務在需要時更能擴充至許多客戶。 Azure Lighthouse 支援各式各樣的管理工作 [和案例](cross-tenant-management-experience.md#enhanced-services-and-scenarios) ，可用來為您的客戶提供價值。

如需詳細資訊，請參閱 [將受控服務供應專案發佈至 Azure Marketplace](../how-to/publish-managed-services-offers.md)。

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>使用 Azure Lighthouse 搭配 Azure 受控應用程式

[Azure 受控應用程式](../../azure-resource-manager/managed-applications/overview.md) 是 isv 可以為客戶提供服務的另一種方式。 您可以搭配使用 Azure Lighthouse 和 Azure 受控應用程式，以啟用增強的案例。

如需詳細資訊，請參閱 [Azure Lighthouse 和 Azure 受控應用程式](managed-applications.md)。

## <a name="saas-based-multi-tenant-offerings"></a>以 SaaS 為基礎的多租使用者供應專案

另一種情況是 ISV 將訂用帳戶中的資源裝載在自己的租使用者中，然後使用 Azure Lighthouse 讓客戶存取這些資源。 然後，客戶可以登入自己的租使用者，並視需要存取這些資源。 Isv 會在自己的租使用者中維護其 IP，而且可以使用自己的支援方案來引發租使用者中所裝載解決方案的相關票證，而不是使用客戶的方案。 由於資源位於 ISV 的租使用者中，因此所有動作都可以直接由 ISV 執行，例如登入 Vm、安裝應用程式，以及執行維護工作。

在此案例中，客戶租使用者中的使用者基本上會被授與「管理租使用者」存取權，即使客戶不管理 ISV 的資源也一樣。 因為它們是直接存取 ISV 的租使用者，所以請務必只授與必要的最低許可權，讓客戶不會不慎變更解決方案或其他 ISV 資源。

若要啟用此架構，ISV 需要取得客戶 Azure AD 租使用者中使用者群組的物件識別碼，以及其租使用者識別碼。 ISV 接著會建立將適當許可權授與此使用者群組的 ARM 範本，並將 [其部署在](../how-to/onboard-customer.md) 包含客戶將存取之資源的 ISV 訂用帳戶上。

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- 了解 [Azure 委派的資源管理](azure-delegated-resource-management.md)。