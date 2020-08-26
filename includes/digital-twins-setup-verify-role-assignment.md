---
author: baanders
description: 包含用來在 Azure 數位 Twins 設定中驗證角色指派的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864848"
---
確認您已成功設定角色指派的其中一種方式，是在 [Azure 入口網站](https://portal.azure.com)中查看 Azure 數位 Twins 實例的角色指派。 在 Azure 入口網站中，移至您的 Azure 數位 Twins 實例 (您可以在 [Azure 數位 Twins 實例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 的頁面上查閱，或在入口網站的搜尋列中搜尋其名稱) 。

然後，在「 *存取控制」 (IAM) > 角色指派*下，查看其所有指派的角色。 使用者應該會出現在清單中，其中包含 *Azure 數位 Twins 擁有者 (預覽) *的角色。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 入口網站中查看 Azure 數位 Twins 實例的角色指派":::