---
title: 要求 Just-In-Time 存取
description: 描述 Azure 託管應用程式的發行者如何請求對託管應用程式的及時訪問。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651380"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>為 Azure 託管應用程式啟用和請求即時訪問

託管應用程式的消費者可能不願意授予您對託管資源組的永久存取權限。 作為管理器應用程式的發行者，您可能希望消費者確切知道何時需要訪問託管資源。 為了讓消費者更好地控制對託管資源的訪問，Azure 託管應用程式提供了一個稱為即時 （JIT） 訪問的功能，該功能當前處於預覽狀態。

JIT 訪問使您能夠請求對託管應用程式資源的提升訪問，以便進行故障排除或維護。 您始終對資源具有唯讀存取權限，但在特定時間段內，您可以具有更大的存取權限。

授予存取權限的工作流是：

1. 將託管應用程式添加到市場，並指定 JIT 存取權限可用。

1. 在部署期間，消費者啟用對託管應用程式的該實例的 JIT 訪問。

1. 部署後，消費者可以更改 JIT 訪問的設置。

1. 當您需要疑難排解或更新託管資源時，您會發送訪問請求。

1. 消費者批准您的請求。

本文重點介紹發行者為啟用 JIT 訪問和提交請求而執行的操作。 要瞭解如何批准 JIT 訪問請求，請參閱[在 Azure 託管應用程式中批准及時訪問](approve-just-in-time-access.md)。

## <a name="add-jit-access-step-to-ui"></a>將 JIT 訪問步驟添加到 UI

CreateUiDefinition.json 檔與您為永久訪問創建的 UI 檔完全相同，只不過您必須包含允許消費者啟用 JIT 訪問的步驟。 要瞭解有關在 Azure 應用商店中發佈第一個託管應用程式產品的更多資訊，請參閱[應用商店中的 Azure 託管應用程式](publish-marketplace-app.md)。

要支援您的產品/服務 JIT 功能，請向 CreateUiDefinition.json 檔添加以下內容：

在"步驟"中：

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
 
在"輸出"中：

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT 訪問處於預覽狀態。 JIT 配置的架構可能會在未來的反覆運算中更改。

## <a name="enable-jit-access"></a>啟用 JIT 訪問

在市場中定義產品/服務時，請確保啟用 JIT 存取權限。

1. 登錄到[雲合作夥伴發佈門戶](https://cloudpartner.azure.com)。

1. 提供用於在市場中發佈託管應用程式的值。 選擇 **"是****"以啟用 JIT 訪問？**

   ![啟用 Just-In-Time 存取](./media/request-just-in-time-access/marketplace-enable.png)

您已向 UI 添加了 JIT 配置步驟，並在市場產品中啟用了 JIT 存取權限。 當消費者部署託管應用程式時，他們可以[為其實例打開 JIT 存取權限](approve-just-in-time-access.md#enable-during-deployment)。

## <a name="request-access"></a>要求存取

當您需要訪問消費者的託管資源時，會發送特定角色、時間和持續時間的請求。 然後，消費者必須批准請求。

要發送 JIT 訪問請求：

1. 為需要訪問的託管應用程式選擇**JIT 訪問**。

1. 選擇 **"合格角色****"，** 然後選擇"啟動"列中所需的角色。

   ![啟動訪問請求](./media/request-just-in-time-access/send-request.png)

1. 在 **"啟動角色**"表單上，選擇角色的開始時間和持續時間以處於活動狀態。 選擇 **"啟動"** 以發送請求。

   ![啟動訪問](./media/request-just-in-time-access/activate-access.png) 

1. 查看通知以查看新的 JIT 請求已成功發送給消費者。

   ![通知](./media/request-just-in-time-access/in-progress.png)

   現在，您必須等待消費者[批准您的請求](approve-just-in-time-access.md#approve-requests)。

1. 要查看託管應用程式的所有 JIT 請求的狀態，請選擇**JIT 訪問**和**請求歷史記錄**。

   ![檢視狀態](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>已知問題

請求 JIT 訪問的帳戶的主 ID 必須顯式包含在託管應用程式定義中。 帳戶不能僅通過包中指定的組進行包含。 此限制將在將來的版本中修復。

## <a name="next-steps"></a>後續步驟

要瞭解如何批准 JIT 訪問請求，請參閱[在 Azure 託管應用程式中批准及時訪問](approve-just-in-time-access.md)。
