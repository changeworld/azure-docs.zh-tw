---
title: 要求 Just-In-Time 存取
description: 描述 Azure 受控應用程式的發行者如何要求即時存取受控應用程式。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651380"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>啟用並要求及時存取 Azure 受控應用程式

受控應用程式的取用者可能不願意授與您對受控資源群組的永久存取權。 身為「管理員」應用程式的「發行者」，您可能會想要讓取用者確實知道何時需要存取受控資源。 為了讓取用者更進一步控制授與受管理資源的存取權，Azure 受控應用程式提供稱為即時（JIT）存取的功能，目前為預覽狀態。

JIT 存取可讓您對受控應用程式的資源要求更高的存取權，以進行疑難排解或維護。 您一律會擁有資源的唯讀存取權，但在特定期間內，您可以擁有更高的存取權。

授與存取權的工作流程為：

1. 您可以將受控應用程式新增至 marketplace，並指定可以使用 JIT 存取。

1. 在部署期間，取用者會針對 managed 應用程式的該實例啟用 JIT 存取。

1. 部署之後，取用者可以變更 JIT 存取的設定。

1. 當您需要對受控資源進行疑難排解或更新時，您會傳送要求以進行存取。

1. 取用者核准您的要求。

本文著重于發行者啟用 JIT 存取和提交要求所採取的動作。 若要瞭解如何核准 JIT 存取要求，請參閱[在 Azure 受控應用程式中核准即時存取](approve-just-in-time-access.md)。

## <a name="add-jit-access-step-to-ui"></a>將 JIT 存取步驟加入至 UI

您的 CreateUiDefinition.json 檔案與您為永久存取建立的 UI 檔案完全相同，不同之處在于您必須包含可讓取用者啟用 JIT 存取的步驟。 若要深入瞭解如何在 Azure Marketplace 中發佈您的第一個受控應用程式供應專案，請參閱[Marketplace 中的 Azure 受控應用程式](publish-marketplace-app.md)。

若要為您的供應專案支援 JIT 功能，請將下列內容新增至您的 CreateUiDefinition.js檔案：

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
> JIT 存取處於預覽狀態。 JIT 設定的架構在未來的反復專案中可能會變更。

## <a name="enable-jit-access"></a>啟用 JIT 存取

在 marketplace 中定義您的供應專案時，請確定您已啟用 JIT 存取。

1. 登入[Cloud Partner 發佈入口網站](https://cloudpartner.azure.com)。

1. 提供值，以在 marketplace 中發佈受控應用程式。 針對 [**啟用 JIT 存取** **] 選取 [是]**

   ![啟用 Just-In-Time 存取](./media/request-just-in-time-access/marketplace-enable.png)

您已將 JIT 設定步驟新增至 UI，並已在 marketplace 供應專案中啟用 JIT 存取。 當取用者部署受控應用程式時，他們可以[開啟其實例的 JIT 存取](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>要求存取

當您需要存取取用者的受控資源時，您會傳送特定角色、時間和持續時間的要求。 然後，取用者必須核准要求。

若要傳送 JIT 存取要求：

1. 針對您需要存取的受控應用程式，選取 [ **JIT 存取**]。

1. 選取 [**合格角色**]，然後選取您想要之角色的 [動作] 資料行中的 [**啟用**]。

   ![啟用存取要求](./media/request-just-in-time-access/send-request.png)

1. 在 [**啟用角色**] 表單上，選取角色啟用的開始時間和持續時間。 選取 [**啟用**] 以傳送要求。

   ![啟用存取](./media/request-just-in-time-access/activate-access.png) 

1. 查看通知，確認新的 JIT 要求已成功傳送給取用者。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   現在，您必須等候取用者[核准您的要求](approve-just-in-time-access.md#approve-requests)。

1. 若要查看 managed 應用程式所有 JIT 要求的狀態，請選取 [ **JIT 存取**和**要求記錄**]。

   ![檢視狀態](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知問題

要求 JIT 存取的帳戶主體識別碼必須明確包含在受控應用程式定義中。 帳戶不能只包含在封裝中指定的群組。 未來的版本將會修正這項限制。

## <a name="next-steps"></a>後續步驟

若要瞭解如何核准 JIT 存取的要求，請參閱[在 Azure 受控應用程式中核准即時存取](approve-just-in-time-access.md)。
