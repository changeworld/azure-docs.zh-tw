---
title: 核准 Just-In-Time 存取
description: 描述 Azure 受控應用程式的取用者如何核准要求，以即時存取受控應用程式。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651016"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>設定和核准 Azure 受控應用程式的及時存取

身為受控應用程式的取用者，您可能不會對發行者提供受控資源群組的永久存取權。 為了讓您更進一步控制授與受管理資源的存取權，Azure 受控應用程式提供稱為即時（JIT）存取的功能，目前為預覽狀態。 它可讓您核准發行者有權存取資源群組的時間和時機。 發行者在這段時間內可能會進行必要的更新，但當該時間結束時，發行者的存取就會過期。

授與存取權的工作流程為：

1. 發行者會將受控應用程式新增至 marketplace，並指定可以使用 JIT 存取。

1. 在部署期間，您會為受控應用程式的實例啟用 JIT 存取。

1. 部署之後，您可以變更 JIT 存取的設定。

1. 發行者會傳送要求以進行存取。

1. 您會核准要求。

本文著重于取用者啟用 JIT 存取和核准要求所採取的動作。 若要瞭解如何使用 JIT 存取發行受控應用程式，請參閱[在 Azure 受控應用程式中要求即時存取](request-just-in-time-access.md)。

> [!NOTE]
> 若要使用即時存取，您必須擁有[Azure Active Directory P2 授權](../../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="enable-during-deployment"></a>在部署期間啟用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 尋找已啟用 JIT 之受控應用程式的 marketplace 專案。 選取 [建立]。

1. 針對新的 managed 應用程式提供值時， **JIT**設定步驟可讓您啟用或停用受控應用程式的 jit 存取。 針對 [**啟用 JIT 存取** **] 選取 [是]** 。 針對在 marketplace 中啟用 JIT 定義的 managed 應用程式，預設會選取此選項。

   ![設定存取權](./media/approve-just-in-time-access/configure-jit-access.png)

   您只能在部署期間啟用 JIT 存取。 如果您選取 [**否**]，發行者會取得受控資源群組的永久存取權。 您稍後無法啟用 JIT 存取。

1. 若要變更預設核准設定，請選取 [**自訂 JIT**設定]。

   ![自訂存取權](./media/approve-just-in-time-access/customize-jit-access.png)

   根據預設，啟用 JIT 的受控應用程式具有下列設定：

   * 核准模式-自動
   * 最大存取持續時間–8小時
   * 核准者–無

   當核准模式設定為 [**自動**] 時，核准者會收到每個要求的通知，但會自動核准要求。 當設定為 [**手動**] 時，核准者會收到每個要求的通知，其中一個必須核准。

   [啟用最長持續時間] 會指定發行者可以要求存取受控資源群組的最大時間量。

   核准者清單是可核准 JIT 存取要求的 Azure Active Directory 使用者。 若要新增核准者，請選取 [**新增核准者**]，然後搜尋使用者。

   更新設定之後，請選取 [**儲存**]。

## <a name="update-after-deployment"></a>部署後更新

您可以變更要求核准方式的值。 不過，如果您未在部署期間啟用 JIT 存取，則稍後無法加以啟用。

若要變更已部署之受控應用程式的設定：

1. 在入口網站中，選取 [管理應用程式]。

1. 選取 [ **JIT**設定]，並視需要變更設定。

   ![變更存取設定](./media/approve-just-in-time-access/change-settings.png)

1. 完成時，選取 [儲存]****。

## <a name="approve-requests"></a>核准要求

當發行者要求存取權時，您就會收到要求的通知。 您可以直接透過受控應用程式，或透過 Azure AD Privileged Identity Management 服務，在所有受控應用程式中核准 JIT 存取要求。 若要使用即時存取，您必須擁有[Azure Active Directory P2 授權](../../active-directory/privileged-identity-management/subscription-requirements.md)。

若要透過受控應用程式核准要求：

1. 針對受控應用程式選取 [ **JIT 存取**]，然後選取 [**核准要求**]。

   ![核准要求](./media/approve-just-in-time-access/approve-requests.png)
 
1. 選取要核准的要求。

   ![選取要求](./media/approve-just-in-time-access/select-request.png)

1. 在表單中，提供核准的原因，然後選取 [**核准**]。

若要透過 Azure AD Privileged Identity Management 核准要求：

1. 選取 [**所有服務**]，然後開始搜尋**Azure AD Privileged Identity Management**。 從可用的選項中選取它。

   ![搜尋服務](./media/approve-just-in-time-access/search.png)

1. 選取 [**核准要求**]。

   ![選取 [核准要求]](./media/approve-just-in-time-access/select-approve-requests.png)

1. 選取 [ **Azure 受控應用程式**]，然後選取要核准的要求。

   ![選取要求](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 JIT 存取發行受控應用程式，請參閱[在 Azure 受控應用程式中要求即時存取](request-just-in-time-access.md)。
