---
author: baanders
description: 包含用來驗證 Azure 數位 Twins 設定中角色指派的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096219"
---
若要檢查您是否已成功設定角色指派，其中一種方式是在 Azure 入口網站中，查看 Azure 數位 Twins 實例的角色指派。 從[Azure 數位 Twins 實例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)的入口網站頁面中，選取您想要檢查的實例名稱。 然後，在 *[存取控制（IAM）] > [角色指派*] 底下，查看其指派的所有角色。 使用者應該會顯示在清單中，其中包含*Azure 數位 Twins 擁有者（預覽）* 的角色。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="在 Azure 入口網站中查看 Azure 數位 Twins 實例的角色指派":::