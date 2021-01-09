---
title: 與 Azure Purview 帳戶連線 
description: 將 Azure Purview 帳戶連線至 Synapse 工作區。
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918459"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>快速入門： 將 Azure Purview 帳戶連線至 Synapse 工作區 

> [!IMPORTANT]
> Azure Synapse Analytics 與 Azure Purview 之間的整合目前為預覽狀態。 如果您有興趣試用 Synapse 中的 Azure Purview，請與您的 Microsoft 業務代表聯繫。

在本快速入門中，您會將 Azure Purview 帳戶註冊到 Synapse 工作區。 該連線可讓您探索 Azure Purview 資產，並透過 Synapse 功能與其互動。 

您可以在 Synapse 中執行下列工作： 
- 使用頂端的搜尋方塊，以關鍵字為基礎來尋找 Purview 資產 
- 了解以中繼資料、資料譜系、註釋為基礎的資料 
- 使用連結的服務或整合資料集將這些資料連線到您的工作區 
- 使用 Synapse Apache Spark、Synapse SQL 和資料流程來分析這些資料集 

## <a name="prerequisites"></a>先決條件 
- [Azure Purview 帳戶](../../purview/create-catalog-portal.md) 
- [Synapse 工作區](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>登入 Synapse 工作區 

移至 https://web.azuresynapse.net 並登入您的工作區。 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>連線 Azure Purview 帳戶的權限 

- 若要將 Azure Purview 帳戶連線到 Synapse 工作區，您需要 Azure 入口網站 IAM 的 Synapse 工作區中的 **參與者** 角色，而且您需要該 Azure Purview 帳戶的存取權。

## <a name="connect-an-azure-purview-account"></a>與 Azure Purview 帳戶連線  

- 在 Synapse 工作區中，移至 [管理]  ->  [Azure Purview]。 選取 [連線到 Purview 帳戶]。 
- 您可以 **從 Azure 訂用帳戶** 選擇，或 **手動輸入**。 **從 Azure 訂用帳戶** 中，您可以選取您有權存取的帳戶。 
- 連線之後，您應該可以在 [Azure Purview 帳戶] 索引標籤中看到 Purview 帳戶的名稱。 
- 您可以使用 Synapse 工作區正上方的 [搜尋] 列來搜尋資料。 

## <a name="nextsteps"></a>後續步驟 

[在 Azure Purview 中註冊及掃描 Azure Synapse 資產](../../purview/register-scan-azure-synapse-analytics.md)

[使用 Azure Purview 探索、連線及瀏覽 Synapse 中的資料](how-to-discover-connect-analyze-azure-purview.md)   