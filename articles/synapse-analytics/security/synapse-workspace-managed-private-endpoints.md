---
title: 受控私人端點
description: 說明 Azure Synapse Analytics 中受控私人端點的文章
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 2d2b65261e09d056ec76b25d6fcb6627bc54770b
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165717"
---
# <a name="synapse-managed-private-endpoints"></a>Synapse 受控私人端點

本文會說明 Azure Synapse Analytics 中的受控私人端點。

## <a name="managed-private-endpoints"></a>受控私人端點

受控私人端點是在與您的 Azure Synapse 工作區相關聯的受控虛擬網路中建立的私人端點。 受控私人端點會建立 Azure 資源的私人連結。 Azure Synapse 會代表您管理這些私人端點。 您可以從 Azure Synapse 工作區建立受控私人端點以存取 Azure 服務 (例如 Azure 儲存體和 Azure Cosmos DB) 和 Azure 託管的客戶/合作夥伴服務。

當您使用受控私人端點時，Azure Synapse 工作區與其他 Azure 資源之間的流量會完全周遊 Microsoft 骨幹網路。 受控私人端點會防止資料外泄。 受控私人端點會使用來自受控虛擬網路的私人 IP 位址，有效地將與 Azure Synapse 工作區通訊的 Azure 服務帶到您的虛擬網路中。 受控私人端點會對應至 Azure 中的特定資源，而不是整個服務。 客戶可以限制其組織所核准之特定資源的連線能力。 

深入了解[私人連結和私人端點](../../private-link/index.yml)。

>[!IMPORTANT]
>只有在具有受控工作區虛擬網路的 Azure Synapse 工作區中，才支援受控私人端點。

>[!NOTE]
>建立 Azure Synapse 工作區時，您可以選擇將受控虛擬網路與其建立關聯。 如果您選擇讓受控虛擬網路與您的工作區相關聯，也可以選擇將工作區的輸出流量限制為僅限核准的目標。 您必須將受控私人端點建立至這些目標。 


當您在 Azure Synapse 中建立受控私人端點時，會以「擱置」狀態建立私人端點連線。 核准工作流程已啟動。 私人連結資源的擁有者會負責核准或拒絕連線。 如果擁有者核准連線，就會建立私人連結。 但是，如果擁有者未核准連線，則不會建立私人連結。 無論是哪一種情況，受控私人端點都會以連線狀態來更新。 只有已核准狀態中的受控私人端點可用來將流量傳送至連結到受控私人端點的私人連結資源。

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>專用 SQL 集區和無伺服器 SQL 集區的受控私人端點

專用 SQL 集區和無伺服器 SQL 集區是 Azure Synapse 工作區中的分析功能。 這些功能會使用未部署到[受控工作區虛擬網路](./synapse-workspace-managed-vnet.md)中的多租用戶基礎結構。

建立工作區時，Azure Synapse 會在該工作區中建立兩個受控私人端點，一個供專用 SQL 集區使用，另一個供無伺服器 SQL 集區使用。 

這兩個受控私人端點會列在 Synapse Studio 中。 選取左側導覽中的 [管理]，然後選取 [受控私人端點] 以在 Studio 中查看。

以 SQL 集區為目標的受控私人端點稱為 *synapse-ws-sql--\<workspacename\>* ，而以無伺服器 SQL 集區為目標的受控私人端點則稱為 *synapse-ws-sqlOnDemand--\<workspacename\>* 。

![專用 SQL 集區和無伺服器 SQL 集區的受控私人端點](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

建立 Azure Synapse 工作區時，系統會自動為您建立這兩個受控私人端點。 這兩個受控私人端點不會收費。

## <a name="next-steps"></a>後續步驟

若要深入了解，請前進到[建立資料來源的受控私人端點](./how-to-create-managed-private-endpoints.md)一文。