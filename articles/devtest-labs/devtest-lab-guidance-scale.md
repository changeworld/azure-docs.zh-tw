---
title: 相應增加 Azure DevTest Labs 基礎結構
description: 此文章提供相應增加 Azure DevTest Labs 基礎結構的指導方針。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478785"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>相應增加 Azure DevTest Labs 基礎結構
在企業級規模實作 DevTest Labs 之前，有數個重要決策點。 了解這些高層的決策點可協助組織未來設計決策。 不過，這些決策點應該不會妨礙組織開始概念證明。 初始相應增加計劃的前三個領域如下：

- 網路和安全性
- 訂用帳戶拓撲
- 角色和職責

## <a name="networking-and-security"></a>網路和安全性
網路和安全性是所有組織的基石。 雖然整個企業的部署需要更深入的分析，但是成功完成概念證明的需求會減少。 一些主要領域包括：

- **Azure 訂用帳戶** – 若要部署 DevTest Labs，您必須使用適當的權限存取 Azure 訂用帳戶，才能建立資源。 有數種方式可以取得 Azure 訂用帳戶的存取權，包括 Enterprise 合約和隨用隨付方案。 如需有關取得 Azure 訂用帳戶存取權的詳細資訊，請參閱 [Azure 企業授權](https://azure.microsoft.com/pricing/enterprise-agreement/)。
- **內部部署資源的存取權** – 部分組織需要其在 DevTest Labs 中的資源能夠存取內部部署資源。 內部部署環境到 Azure 需要有安全連線。 因此，在開始使用之前設定 VPN 或 ExpressRoute 連線非常重要。 如需詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。
- **其他安全性需求** – 其他安全性需求，例如電腦原則、公用 IP 位址存取權，連線到網際網路都是必須要審核過後再實作概念證明的案例。 

## <a name="subscription-topology"></a>訂用帳戶拓撲
訂用帳戶拓撲是在將 DevTest Labs 部署至企業時的重要設計考量。 不過，不需要完成概念證明，就可以鞏固所有決策。 評估企業實作所需的訂用帳戶數目時，有兩個極端的方法： 

- 整個組織使用一個訂用帳戶
- 每位使用者使用一個訂用帳戶

接下來，我們強調每一種方法的優點。

### <a name="one-subscription"></a>一個訂用帳戶
一個訂用帳戶的方法通常在大型企業中不容易管理。 不過，限制訂用帳戶的數目有下列優點：

- **預測**企業的成本。  單一訂用帳戶的預算變得更容易，因為所有資源都在單一集區中。 此方法更容易決定在計費期間的任何指定時間執行成本控制措施。
- Vm、成品、公式、網路設定、許可權、原則等的**管理性**更容易，因為只有一個訂用帳戶中的所有更新都需要，而不是在多個訂用帳戶之間進行更新。
- 對於需要內部部署連線的企業，單一訂用帳戶的**網路**工作大幅簡化。 額外的訂用帳戶需要跨訂用帳戶連接虛擬網路 (中樞-支點模型)，而這需要額外的設定、管理、IP 位址空間等。
- 當每個人都使用相同的訂用帳戶時，**小組共同作業**更容易，例如，更容易將 VM 重新指派給同事、共用小組資源等。

### <a name="subscription-per-user"></a>每位使用者使用一個訂用帳戶
每位使用者使用一個訂用帳戶可為替代範圍提供相等的機會。 擁有許多訂用帳戶的優點包括：

- **Azure 調整配額**不會妨礙採用。 例如，撰寫本文時，Azure 允許每個訂用帳戶有 200 個儲存體帳戶。 在 Azure 中，大部分的服務都有操作配額 (許多可自訂，某些則不行)。 如果是每位使用者使用一個訂用帳戶這個模型，就不太可能達到大部分的配額。 如需有關目前 Azure 調整配額的詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- **退款**給群組或個別開發人員變得更容易，因為組織能夠使用其目前的模型來計算成本。
- DevTest Labs 環境的**擁有權和權限**很簡單。 您為開發人員提供訂用帳戶層級的存取權，他們會完全負責所有事情，包括網路設定、實驗室原則和 VM 管理。

在企業中，對範圍的極端情況可能會有足夠的限制。 因此，您可能需要使用適中的方式設定訂用帳戶。 最佳做法是，組織的目標應該是盡可能使用最小數量的訂用帳戶，同時記住可增加訂用帳戶總數的強制函式。 再次提醒您，訂用帳戶拓撲對於 DevTest Labs 的企業部署至關重要，但不應該延遲概念證明。 在[控管](devtest-lab-guidance-governance-policy-compliance.md)一文中包含如何決定組織中的訂用帳戶和實驗室細微度的其他詳細資料。

## <a name="roles-and-responsibilities"></a>角色和職責
DevTest Labs 概念證明有三個主要角色及已定義的職責，分別是訂用帳戶擁有者、DevTest Labs 擁有者、DevTest Labs 使用者和 (選用的) 參與者。

- **訂** 用帳戶擁有者-訂用帳戶擁有者有權管理 Azure 訂用帳戶，包括指派使用者、管理原則、建立 & 管理網路拓撲、要求增加配額等。如需詳細資訊，請參閱 [這篇文章](../role-based-access-control/rbac-and-directory-admin-roles.md)。
- **DevTest Labs 擁有者** – DevTest Labs 擁有者對實驗室有完整的系統管理存取權。 這位人員會負責新增/移除使用者、管理成本設定、一般實驗室設定，以及其他以 VM/成品為基礎的工作。 實驗室擁有者也具備 DevTest Labs 使用者的所有權限。
- **DevTest Labs 使用者** – DevTest Labs 使用者可以建立和使用實驗室中的虛擬機器。 這些人員對於所建立的 VM 有一些基本的系統管理功能 (啟動/停止/刪除/設定其 VM)。 這些使用者無法管理其他使用者的 VM。

## <a name="next-steps"></a>接下來的步驟
請參閱本系列中的下一篇文章：[協調 Azure DevTest Labs 的實作](devtest-lab-guidance-orchestrate-implementation.md)