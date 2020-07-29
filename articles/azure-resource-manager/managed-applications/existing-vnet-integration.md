---
title: 部署至現有的虛擬網路
description: 說明如何讓受控應用程式的使用者選取現有的虛擬網路。 虛擬網路可以位於受控應用程式以外。
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260681"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>搭配 Azure 受控應用程式使用現有的虛擬網路

本文說明如何定義 Azure 受控應用程式，以在取用者的訂用帳戶中與現有的虛擬網路整合。 受控應用程式可讓取用者決定是要建立新的虛擬網路，還是使用現有的。 現有的虛擬網路可以位於受控資源群組外部。

## <a name="main-template"></a>主要的範本

首先，我們來看一下檔案**上的mainTemplate.js** 。 用於部署虛擬機器及其相關聯資源的整個範本如下所示。 稍後，您將會更仔細地檢查與使用現有虛擬網路相關的範本部分。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

請注意，已有[條件地部署](../templates/conditional-resource-deployment.md)虛擬網路。 取用者會傳入參數值，指出是否要建立新的或使用現有的虛擬網路。 如果取用者選取新的虛擬網路，則會部署資源。 否則，在部署期間會略過資源。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

虛擬網路識別碼的變數有兩個屬性。 部署新的虛擬網路時，一個屬性會傳回資源識別碼。 當使用現有的虛擬網路時，另一個屬性會傳回資源識別碼。 現有虛擬網路的資源識別碼包括包含虛擬網路的資源組名。

子網識別碼是從虛擬網路識別碼的值所構成。 其使用值符合取用者選取專案。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

網路介面設定為 [子網識別碼] 變數。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI 定義

現在，讓我們查看檔案**上的createUiDefinition.js** 。 整個檔案為：

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

檔案包含虛擬網路元素。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

該元素可讓取用者選取新的或現有的虛擬網路。

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="新的或現有的虛擬網路":::

在輸出中，您會包含一個值，指出取用者是否選取了新的或現有的虛擬網路。 另外還有一個受控識別值。

> [!NOTE]
> 受控識別的輸出值必須命名為**microsoft.managedidentity**。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何建立 UI 定義檔，請參閱[適用于 Azure 受控應用程式的建立體驗的CreateUiDefinition.js](create-uidefinition-overview.md)。
