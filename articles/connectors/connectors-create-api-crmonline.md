---
title: 連接到 Dynamics 365
description: 使用 Azure Logic Apps 建立和管理 Dynamics 365 記錄
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82994315"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 建立及管理 Dynamics 365 中的記錄

Dynamics 365 使用[Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)。 若要連線至 Dynamics 365，請使用[Common Data Service 連接器](https://docs.microsoft.com/connectors/commondataservice/)。

> [!IMPORTANT]
> [Dynamics 365 連接器](https://docs.microsoft.com/connectors/dynamicscrmonline/)已淘汰，但會繼續運作，直到移除為止。 請勿將 Dynamics 365 連接器用於新的邏輯應用程式。 尚未宣佈移除 Dynamics 365 連接器的時程表。 您不需要將現有的邏輯應用程式轉換為 Common Data Service 連接器或其他已規劃的新連接器，但必須在移除連接器時轉換邏輯應用程式。 如需詳細資訊，請參閱[Dynamics 365 Connector 已淘汰](https://docs.microsoft.com/power-platform/important-changes-coming)。
>
> [Common Data Service 連接器](https://docs.microsoft.com/connectors/commondataservice/)提供與已取代的 Dynamics 365 連接器相同的功能，但包含可提升可靠性的改善項目。 如需在邏輯應用程式中使用 Common Data Service 連接器的詳細資訊，請參閱[使用 Azure Logic Apps 建立和管理 Common Data Service 記錄](../connectors/connect-common-data-service.md)。

如需 Common Data Service 的詳細資訊，請參閱下列主題：

* [瞭解：開始使用 Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [瞭解：使用 Power 平臺和 Common Data Service 來連接和分析您的 Dynamics 365 資料](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)