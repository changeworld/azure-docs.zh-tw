---
title: 管理工作區
titleSuffix: ML Studio (classic) - Azure
description: 管理對 Azure 機器學習工作室（經典）工作區的訪問，並部署和管理機器學習 API Web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217957"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>管理 Azure 機器學習工作室（經典）工作區

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> 如需在 Machine Learning Web 服務入口網站中管理 Web 服務的相關資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。
> 
> 

您可以在 Azure 門戶中管理機器學習工作室（經典）工作區。



## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

要在 Azure 門戶中管理 Studio（經典）工作區，可以：

1. 使用 Azure 訂用帳戶系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頁面頂部的搜索框中，輸入"機器學習工作室（經典）工作區"，然後選擇**機器學習工作室（經典）工作區**。
3. 按一下您想要管理的工作區。

除了標準的資源管理資訊和可用的選項，您還可以︰

- 檢視**屬性** - 此頁面會顯示工作區和資源的資訊，而且您可以變更這個工作區所連線的訂用帳戶和資源群組。
- **重新同步儲存體金鑰** - 工作區會保有儲存體帳戶的金鑰。 如果儲存體帳戶變更了金鑰，則您可以按一下 [重新同步金鑰]**** 來與工作區同步處理金鑰。

要管理與此 Studio（經典）工作區關聯的 Web 服務，請使用機器學習 Web 服務門戶。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新的 Web 服務，您必須獲得下列角色的指派：要部署 Web 服務之訂用帳戶上的參與者或系統管理員角色。 如果邀請其他使用者加入機器學習 Studio（經典）工作區，則必須將它們分配給訂閱上的參與者或管理員角色，然後才能部署或管理 Web 服務。 
> 
>如需如何設定存取權限的詳細資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟
* 深入了解[使用Azure Resource Manager 範本部署 Machine Learning](deploy-with-resource-manager-template.md)。 
