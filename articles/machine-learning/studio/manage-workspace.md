---
title: ML Studio （傳統）：管理工作區-Azure
description: 管理 Azure Machine Learning Studio （傳統）工作區的存取權，以及部署和管理 Machine Learning API web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 6f3661956c61eb20f4c336f8644f85eb7e1b4100
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433131"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>管理 Azure Machine Learning Studio （傳統）工作區

**適用物件：** ![是 ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio （傳統） ![ 否](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


> [!NOTE]
> 如需在 Machine Learning Web 服務入口網站中管理 Web 服務的相關資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。
> 
> 

您可以在 Azure 入口網站中管理 Machine Learning Studio （傳統）工作區。



## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

若要管理 Azure 入口網站中的 Studio （傳統）工作區：

1. 使用 Azure 訂用帳戶系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頁面頂端的 [搜尋] 方塊中，輸入 "machine learning Studio （傳統）工作區"，然後選取 [ **Machine Learning Studio （傳統）工作區**]。
3. 按一下您想要管理的工作區。

除了標準的資源管理資訊和可用的選項，您還可以︰

- 檢視**屬性** - 此頁面會顯示工作區和資源的資訊，而且您可以變更這個工作區所連線的訂用帳戶和資源群組。
- **重新同步儲存體金鑰** - 工作區會保有儲存體帳戶的金鑰。 如果儲存體帳戶變更了金鑰，則您可以按一下 [重新同步金鑰]**** 來與工作區同步處理金鑰。

若要管理與此 Studio （傳統）工作區相關聯的 web 服務，請使用 Machine Learning Web 服務入口網站。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新的 Web 服務，您必須獲得下列角色的指派：要部署 Web 服務之訂用帳戶上的參與者或系統管理員角色。 如果您邀請另一位使用者加入 machine learning Studio （傳統）工作區，您必須先將他們指派給訂用帳戶的參與者或系統管理員角色，才能部署或管理 web 服務。 
> 
>如需如何設定存取權限的詳細資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟
* 深入了解[使用Azure Resource Manager 範本部署 Machine Learning](deploy-with-resource-manager-template.md)。 
