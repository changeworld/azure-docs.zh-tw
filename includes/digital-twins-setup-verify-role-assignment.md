---
author: baanders
description: 包含用來驗證 Azure 數位 Twins 設定中角色指派的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405556"
---
若要檢查您是否已成功設定角色指派，其中一種方式是在[Azure 入口網站](https://portal.azure.com)中，查看 Azure 數位 Twins 實例的角色指派。 移至入口網站的[Azure 數位 Twins 實例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)頁面（您可以使用此連結，或在入口網站搜尋列中搜尋），然後選取您要檢查之實例的名稱。 

然後，在 *[存取控制（IAM）] > [角色指派*] 底下，查看其指派的所有角色。 使用者應該會顯示在清單中，其中包含*Azure 數位 Twins 擁有者（預覽）* 的角色。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 入口網站中查看 Azure 數位 Twins 實例的角色指派":::