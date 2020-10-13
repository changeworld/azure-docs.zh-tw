---
title: 部署至現有的虛擬網路
description: 說明如何讓您的受控應用程式使用者選取現有的虛擬網路。 虛擬網路可以在受控應用程式之外。
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260681"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>使用現有的虛擬網路搭配 Azure 受控應用程式

本文說明如何定義 Azure 受控應用程式，以與取用者訂用帳戶中現有的虛擬網路整合。 受控應用程式可讓取用者決定是否要建立新的虛擬網路，或使用現有的虛擬網路。 現有的虛擬網路可以位於受控資源群組之外。

## <a name="main-template"></a>主要的範本

首先，讓我們看看檔案 ** 上的mainTemplate.js** 。 用來部署虛擬機器和其相關聯資源的整個範本如下所示。 稍後，您將更仔細地檢查與使用現有虛擬網路相關的範本元件。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

請注意，會有 [條件地部署](../templates/conditional-resource-deployment.md)虛擬網路。 取用者會傳入參數值，指出是要建立新的或使用現有的虛擬網路。 如果取用者選取新的虛擬網路，則會部署資源。 否則，會在部署期間略過資源。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

虛擬網路識別碼的變數有兩個屬性。 當部署新的虛擬網路時，其中一個屬性會傳回資源識別碼。 當使用現有的虛擬網路時，其他屬性會傳回資源識別碼。 現有虛擬網路的資源識別碼包含包含虛擬網路的資源組名。

子網識別碼是由虛擬網路識別碼的值所構成。 它會使用值與取用者選取專案相符。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

網路介面設定為子網識別碼變數。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI 定義

現在，讓我們看看檔案 ** 上的createUiDefinition.js** 。 整個檔案如下：

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

檔案包含虛擬網路元素。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

該元素可讓取用者選取新的或現有的虛擬網路。

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="新的或現有的虛擬網路":::

在輸出中，您會包含一個值，指出取用者是否已選取新的或現有的虛擬網路。 此外，也有受控識別值。

> [!NOTE]
> 受控識別的輸出值必須命名為 **managedIdentity**。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>接下來的步驟

若要深入瞭解如何建立 UI 定義檔，請參閱 [ Azure 受控應用程式建立體驗的CreateUiDefinition.json](create-uidefinition-overview.md)。
