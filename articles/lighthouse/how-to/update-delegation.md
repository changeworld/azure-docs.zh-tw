---
title: 更新委派
description: 瞭解如何更新客戶先前上線至 Azure Lighthouse 的委派。
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: e204d1f3546e6e978f91c7e808065a388a4af4b3
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093412"
---
# <a name="update-a-delegation"></a>更新委派

將訂用帳戶上線 (或資源群組) 至 Azure Lighthouse 之後，您可能需要進行變更。 例如，您的客戶可能會想要執行其他需要不同 Azure 內建角色的管理工作，或者您可能需要變更客戶訂用帳戶委派的租使用者。

> [!TIP]
> 雖然我們會在本主題中參考服務提供者和客戶，但 [管理多個](../concepts/enterprise.md) 租使用者的企業可以使用相同的程式來設定 Azure Lighthouse 以及合併其管理經驗。

如果您 [透過 Azure Resource Manager 範本 (ARM 範本) 來上線客戶 ](onboard-customer.md)，則必須為該客戶執行新的部署。 根據您要變更的專案，您可能會想要更新原始供應專案，或移除原始供應專案並建立新的供應專案。

- **如果您只變更授權**：您可以只變更 ARM 範本的 **授權** 區段來更新委派。
- **如果您要變更管理租使用者**：您必須使用與先前供應專案不同的 **>mspoffername** 來建立新的 ARM 範本。

## <a name="update-your-arm-template"></a>更新 ARM 範本

若要更新您的委派，您將需要部署 ARM 範本，其中包含您想要進行的變更。

如果您只是更新授權 (例如新增具有您先前未包含之角色的新使用者群組，或是變更現有使用者) 的角色，則可以使用與先前委派所用 [ARM 範本](onboard-customer.md#create-an-azure-resource-manager-template)相同的 **>mspoffername** 。 您可以使用先前的範本作為起點。 然後，進行您需要的變更，例如將一個 Azure 內建角色替換成另一個角色，或將全新的授權新增至範本。

如果您變更 **>mspoffername**，則會被視為新的個別供應專案。 如果您要變更管理租使用者，則這是必要的。

如果管理租使用者保持不變，則不需要變更 **>mspoffername** 。 在大部分情況下，我們建議讓相同的客戶和管理租使用者只能使用一個 **>mspoffername** 。 如果您選擇改為變更，請確定先移除客戶的先前委派，再部署新的委派。

## <a name="remove-the-previous-delegation"></a>移除先前的委派

在執行新的部署之前，您可能會想要 [移除先前委派的存取權](remove-delegation.md)。 這可確保移除所有先前的許可權，讓您可以使用應繼續進行的確切使用者/群組和角色來開始進行清除。

> [!IMPORTANT]
> 如果您使用新的 **>mspoffername** 並保留任何相同的 **principalId** 值，您必須先移除先前委派的存取權，然後再部署新的供應專案。 如果您未先移除該供應專案，則先前授與許可權的使用者可能會因為指派衝突而完全遺失存取權。

如果您要變更管理租使用者，如果您想要讓這兩個租使用者繼續具有存取權，您可以將先前的供應專案保留在原處。 如果您只想要讓新的管理租使用者具有存取權，則必須移除先前的供應專案。 這可以在您將新的供應專案上線之前或之後完成。

如果您要更新供應專案以僅調整授權，並保留相同的 **>mspoffername**，則不需要移除先前的委派。 新的部署將會取代先前的委派，而且只會套用最新範本中的授權。

:::image type="content" source="../media/update-delegation.jpg" alt-text="此圖顯示何時要變更 >mspoffername 和移除先前的委派。":::

如果管理租使用者中的任何使用者被授與原始委派中的 [受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ，則移除委派的存取權就可以完成。 如果您管理租使用者中的任何使用者都沒有此角色，則您可以要求客戶 [在 Azure 入口網站中移除該供應專案的存取權](view-manage-service-providers.md#add-or-remove-service-provider-offers)。

> [!TIP]
> 如果您已遵循上述步驟來移除先前的委派，但仍無法部署新的 ARM 範本，您可能需要 [完全移除註冊定義](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)。 這可由客戶租使用者中擁有者角色的任何使用者來完成。  

## <a name="deploy-the-arm-template"></a>部署 ARM 範本

您的客戶可以使用先前執行的相同方式來 [部署更新的範本](onboard-customer.md#deploy-the-azure-resource-manager-templates) ：在 Azure 入口網站、使用 PowerShell，或使用 Azure CLI。

完成部署之後，請 [確認它是否成功](onboard-customer.md#confirm-successful-onboarding)。 更新後的授權就會對客戶已委派的)  (的訂用帳戶或資源群組生效。

## <a name="updating-managed-service-offers"></a>更新受管理的服務供應專案

如果您透過發佈至 Azure Marketplace 的受控服務供應專案來上線客戶，而您想要更新授權，您可以使用您想要在該客戶的方案中更新的[授權](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization)來[發佈新版本的供應](../../marketplace/partner-center-portal/update-existing-offer.md)專案，以更新委派。 接著，客戶將能夠更新至 Azure 入口網站中的最新版本。

如果您想要變更管理租使用者，您將需要 [建立併發布新的受控服務供應](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) 專案，讓客戶接受。

> [!TIP]
> 如先前所述，我們建議您不要在相同的客戶與管理租使用者之間使用多個不同的供應專案。 如果您為相同的客戶發佈的新供應專案使用相同的管理租使用者，請確定已移除先前的供應專案，再讓客戶接受較新的供應專案。

## <a name="next-steps"></a>後續步驟

- 前往 Azure 入口網站中的 [我的客戶]，以[檢視及管理客戶](view-manage-customers.md)。
- 了解如何[移除先前上線的委派存取權](remove-delegation.md)。
