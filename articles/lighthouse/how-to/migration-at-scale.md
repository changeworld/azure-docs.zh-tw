---
title: 使用 Azure Migrate 大規模管理遷移專案
description: 瞭解如何有效地在委派的客戶資源上使用 Azure Migrate。
ms.date: 12/3/2020
ms.topic: how-to
ms.openlocfilehash: 184307814bd3ceae6047734946f79b0ba5cb2e10
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603175"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>使用 Azure Migrate 大規模管理遷移專案

作為服務提供者，您可能已上線多個客戶租使用者來 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 可讓服務提供者一次在多個 Azure Active Directory () Azure AD 之間執行大規模作業，讓管理工作更有效率。

[Azure Migrate](../../migrate/migrate-services-overview.md) 提供集中式的中樞，以評估及遷移至 Azure 內部部署伺服器、基礎結構、應用程式和資料。 通常，針對多個客戶大規模執行評量和遷移的合作夥伴，必須使用 [CSP (雲端解決方案提供者) 訂](/partner-center/customers-revoke-admin-privileges) 用帳戶模型，或 [在客戶租使用者中建立來賓使用者](/azure/active-directory/external-identities/what-is-b2b)，來個別存取每個客戶訂用帳戶。

Azure Lighthouse 與 Azure Migrate 整合，可讓服務提供者大規模探索、評估和遷移不同客戶的工作負載，同時讓客戶擁有其環境的完整可見度和控制權。 透過 Azure 委派的資源管理，服務提供者可以單一查看他們跨多個客戶租使用者所管理的所有 Azure Migrate 專案。

> [!NOTE]
> 透過 Azure Lighthouse，合作夥伴可以針對內部部署 VMware Vm、Hyper-v Vm、實體伺服器和 AWS/GCP 實例執行探索、評估和遷移。 [VMWARE VM 遷移](../../migrate/server-migrate-overview.md)有兩個選項。 目前，在委派的客戶訂用帳戶中處理遷移專案時，只可使用代理程式的遷移方法;使用無代理程式複寫的遷移目前不支援透過委派存取客戶的範圍。

本主題概要說明如何以可調整的方式使用 [Azure Migrate](../../migrate/migrate-services-overview.md) 。

> [!TIP]
> 雖然我們指的是本主題中的服務提供者和客戶，但本指南也適用于 [使用 Azure Lighthouse 來管理多個](../concepts/enterprise.md)租使用者的企業。

視您的案例而定，您可能會想要在客戶租使用者或管理租使用者中建立 Azure Migrate。 請參閱下列考慮，並判斷哪一個模型最適合您客戶的遷移需求。

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>在客戶租使用者中建立 Azure Migrate 專案

使用 Azure Lighthouse 的一個選項是在客戶租使用者中建立 Azure Migrate 專案。 然後，管理租使用者中的使用者就可以在建立遷移專案時選取客戶訂用帳戶。 從管理租使用者中，服務提供者可以執行必要的遷移作業。 這可能包括部署 Azure Migrate 設備以探索工作負載、將 Vm 分組來評定工作負載，以及計算雲端相關成本、查看 VM 就緒程度，以及執行遷移。

在此案例中，系統將不會建立任何資源，也不會儲存在管理租使用者中，即使可以從該租使用者起始和執行探索和評量步驟也是一樣。 所有資源（例如遷移專案、內部內部部署工作負載的評量報告，以及目標目的地的已遷移資源）都會部署在客戶訂用帳戶中。 不過，服務提供者可以從自己的租使用者和入口網站體驗存取所有客戶專案。

這種方法可將服務提供者的內容切換降至最低，以在多個客戶上工作，同時讓客戶將其所有資源保留在自己的租使用者。

此模型的工作流程將如下所示：

1. 客戶已 [上線 Azure Lighthouse](onboard-customer.md)。 將與 Azure Migrate 搭配使用的身分識別需要參與者內建角色。
1. 指定的使用者登入 Azure 入口網站中的管理租使用者，然後移至 Azure Migrate。 此使用者會 [建立 Azure Migrate 專案](/migrate/create-manage-projects.md)，然後選取適當的委派客戶訂用帳戶。
1. 然後，使用者會 [執行探索和評估的步驟](../../migrate/tutorial-discover-vmware.md)。

   針對 VMware Vm，在設定設備之前，您可以限制探索 vCenter Server 的資料中心、叢集、叢集的資料夾、主機、主機的資料夾或個別 Vm。 若要設定範圍，請在設備用來存取 vCenter Server 的帳戶上指派許可權。 如果有多個客戶的 Vm 裝載在程式管理器上，這會很有用。 您無法限制 Hyper-v 的探索範圍。

    > [!NOTE]
    > 您可以透過 Azure Lighthouse 所提供的委派存取權，探索和評估 VMware 虛擬機器以使用 Azure Migrate 進行遷移。 針對 VMware 虛擬機器的遷移，在委派的客戶訂用帳戶中處理遷移專案時，目前只支援以代理程式為基礎的方法。

1. 當目標客戶訂用帳戶就緒時，請透過 Azure Lighthouse 授與的存取權進行遷移。 系統會在客戶租使用者中，于目標訂用帳戶下建立包含評定結果和已遷移資源的遷移專案。

> [!TIP]
> 在遷移之前，必須先部署登陸區域，以布建基礎結構資源，並準備將遷移虛擬機器的訂用帳戶。 若要在此登陸區域中存取或建立某些資源，可能需要擁有者內建角色，但 Azure Lighthouse 目前不支援此角色。 在這類情況下，客戶可能需要提供來賓存取角色，或透過 CSP 訂用帳戶模型委派系統管理員存取權。 如需建立多租使用者登陸區域的方法，請參閱 GitHub 上的 [多租使用者-登陸區域示範解決方案](https://github.com/Azure/Multi-tenant-Landing-Zones) 。

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>在管理租使用者中建立 Azure Migrate 專案

在此案例中，管理租使用者中的使用者仍會執行探索和評量等遷移相關的作業。 不過，「遷移」專案和所有相關資源將位於「合作夥伴」租使用者中，而客戶則不會直接看到專案 (但如果需要) ，可與客戶共用評量。 每位客戶的遷移目的地將會是客戶的訂用帳戶。

這種方法可讓服務提供者快速開始遷移探索和評量專案，從客戶訂用帳戶和租使用者中將這些初始步驟抽象化。

此模型的工作流程將如下所示：

1. 客戶已 [上線 Azure Lighthouse](onboard-customer.md)。 將與 Azure Migrate 搭配使用的身分識別需要參與者內建角色。
1. 指定的使用者登入 Azure 入口網站中的管理租使用者，然後移至 Azure Migrate。 此使用者會在屬於管理租使用者的訂用帳戶中 [建立 Azure Migrate 專案](/migrate/create-manage-projects.md) 。
1. 然後，使用者會 [執行探索和評估的步驟](../../migrate/tutorial-discover-vmware.md)。 內部部署 Vm 將在管理租使用者中建立的遷移專案內進行探索和評估，然後從該處遷移。

   如果您要在相同的 Hyper-v 主機中管理多個客戶，您可以一次探索所有工作負載。 您可以在相同群組中選取客戶專屬的 Vm，然後建立評量，並藉由選取適當的客戶訂用帳戶做為目標目的地來執行遷移。 不需要限制探索範圍，而且您可以在一個遷移專案中全面瞭解所有客戶工作負載。

1. 準備好時，請選取委派的客戶訂用帳戶作為複寫和遷移工作負載的目標目的地，以繼續進行遷移。 新建立的資源將會存在於客戶訂用帳戶中，而與遷移專案相關的評定資料和資源將會保留在管理租使用者中。

## <a name="partner-recognition-for-customer-migrations"></a>客戶遷移的合作夥伴辨識

作為 [Microsoft 合作夥伴網路](https://partner.microsoft.com)的成員，您可以將合作夥伴識別碼連結到用來管理委派客戶資源的認證。 透過合作夥伴系統管理員連結 (PAL) ，Microsoft 可以根據您為客戶執行的工作（包括遷移專案），對您的組織進行屬性影響和 Azure 取用的收益。

如需詳細資訊，請參閱[連結您的合作夥伴識別碼，以追蹤已委派的資源受到的影響](partner-earned-credit.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解 [Azure Migrate](../../migrate/migrate-services-overview.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。

