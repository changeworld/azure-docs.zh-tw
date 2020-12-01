---
title: 要求 Just-In-Time 存取
description: 描述 Azure 受控應用程式的發行者如何要求即時存取受控應用程式。
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: f839eb04e0c036b5059e35fac16d9c702646cbd7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435683"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>啟用和要求即時存取 Azure 受控應用程式

受控應用程式的取用者可能不願意授與您永久存取受控資源群組的許可權。 作為管理員應用程式的發行者，您可能會想要讓取用者確切知道何時需要存取受控資源。 為了讓取用者更能控制授與對 managed 資源的存取權，Azure 受控應用程式提供稱為即時 (JIT) 存取的功能。 此功能目前為預覽狀態。

JIT 存取可讓您對受控應用程式的資源要求更高的存取權，以進行疑難排解或維護。 您一律會擁有資源的唯讀存取權，但在特定期間內，您可以擁有更高的存取權。

授與存取權的工作流程為：

1. 您將受控應用程式新增至 marketplace，並指定可使用 JIT 存取。

1. 在部署期間，取用者會啟用該受控應用程式實例的 JIT 存取。

1. 部署之後，取用者可以變更 JIT 存取的設定。

1. 當您需要針對 managed 資源進行疑難排解或更新時，您可以傳送要求以進行存取。

1. 取用者核准您的要求。

本文著重于發行者啟用 JIT 存取和提交要求所採取的動作。 若要瞭解如何核准 JIT 存取要求，請參閱 Azure 受控應用程式中的 [核准即時存取](approve-just-in-time-access.md)。

## <a name="add-jit-access-step-to-ui"></a>將 JIT 存取步驟新增至 UI

在檔案的 CreateUiDefinition.js中，包含可讓取用者啟用 JIT 存取的步驟。 若要為您的供應專案支援 JIT 功能，請將下列內容新增至您的 CreateUiDefinition.json file。

在 [步驟] 中：

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

在 [輸出] 中：

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT 存取目前為預覽狀態。 JIT 設定的架構可能會在未來的反復專案中變更。

## <a name="enable-jit-access"></a>啟用 JIT 存取

在合作夥伴中心中建立供應專案時，請務必啟用 JIT 存取。

1. 在 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)中登入商用 Marketplace 入口網站。

1. 如需建立新受控應用程式的指引，請遵循 [建立 Azure 應用程式供應](../../marketplace/create-new-azure-apps-offer.md)專案中的步驟。

1. 在 [ **技術** 設定] 頁面上，選取 [ **啟用即時 (JIT) 存取** ] 核取方塊。

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="啟用 Just-In-Time 存取":::

您已在 UI 中新增 JIT 設定步驟，並已在商業 marketplace 供應專案中啟用 JIT 存取。 當取用者部署受控應用程式時，它們可以 [開啟其實例的 JIT 存取權](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>要求存取

當您需要存取取用者的受控資源時，會傳送特定角色、時間和持續時間的要求。 然後，取用者必須核准要求。

若要傳送 JIT 存取要求：

1. 針對您需要存取的受控應用程式，選取 [ **JIT 存取** ]。

1. 選取 [ **符合資格的角色**]，然後針對您想要的角色選取 [動作] 欄中的 [ **啟用** ]。

   ![啟用存取要求](./media/request-just-in-time-access/send-request.png)

1. 在 [ **啟用角色** ] 表單上，選取角色的 [開始時間] 和 [持續時間]。 選取 [ **啟動** ] 以傳送要求。

   ![啟用存取](./media/request-just-in-time-access/activate-access.png) 

1. 查看通知，以查看新的 JIT 要求已成功傳送給取用者。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   現在，您必須等待取用者 [核准您的要求](approve-just-in-time-access.md#approve-requests)。

1. 若要查看 managed 應用程式之所有 JIT 要求的狀態，請選取 [ **JIT 存取** ] 和 [ **要求記錄**]。

   ![檢視狀態](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知問題

要求 JIT 存取的帳戶主體識別碼必須明確包含在受控應用程式定義中。 帳戶不能包含在封裝中指定的群組中。 這項限制將在未來的版本中修正。

## <a name="next-steps"></a>後續步驟

若要瞭解如何核准 JIT 存取的要求，請參閱 [Azure 受控應用程式中的核准即時存取](approve-just-in-time-access.md)。
