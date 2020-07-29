---
title: 使用原則部署受控應用程式的關聯
description: 瞭解如何使用 Azure 原則服務來部署受控應用程式的關聯。
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650938"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>使用 Azure 原則部署受控應用程式的關聯

Azure 原則可以用來部署關聯，以將資源與受控應用程式產生關聯。 在本文中，我們會說明可部署關聯的內建原則，以及您可以如何使用該原則。

## <a name="built-in-policy-to-deploy-associations"></a>用來部署關聯的內建原則

部署受控應用程式的關聯是內建原則，可以用來部署關聯，以將資源與受控應用程式產生關聯。 此原則會接受三個參數：

- 受控應用程式識別碼-此識別碼是資源需要關聯之受控應用程式的資源識別碼。
- 要建立關聯的資源類型-這些資源類型是要與受控應用程式相關聯的資源類型清單。 您可以使用相同的原則，將多個資源類型關聯至受控應用程式。
- 關聯名稱前置詞-此字串是要新增至所建立之關聯資源名稱的前置詞。 預設值為 "DeployedByPolicy"。

此原則會使用 DeployIfNotExists 評估。 它會在資源提供者已處理所選取資源類型的建立或更新資源要求，且評估已傳回成功狀態碼後執行。 之後，就會使用範本部署來部署關聯資源。
如需關聯的詳細資訊，請參閱[Azure 自訂提供者資源](../custom-providers/concepts-resource-onboarding.md)上線

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>如何使用部署關聯內建原則 

### <a name="prerequisites"></a>必要條件
如果受控應用程式需要訂用帳戶的許可權才能執行動作，則在未授與許可權的情況下，關聯資源的原則部署不會運作。

### <a name="policy-assignment"></a>原則指派
若要使用內建原則，請建立原則指派，並為受管理的應用程式原則指派部署關聯。 成功指派原則之後，原則會識別不符合規範的資源，並為這些資源部署關聯。

![指派內建原則](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>取得說明

如果您有關于 Azure 自訂資源提供者開發的問題，請嘗試在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)詢問。 類似的問題可能已有人回答，因此請先查看再張貼問題。 新增標記 ```azure-custom-providers``` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

在本文中，您會學會有關使用內建原則來部署關聯的資訊。 若要深入瞭解，請參閱下列文章：

- [概念： Azure 自訂提供者資源上線](../custom-providers/concepts-resource-onboarding.md)
- [教學課程：使用自訂提供者的資源上線](../custom-providers/tutorial-resource-onboarding.md)
- [教學課程：在 Azure 中建立自訂動作和資源](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [快速入門：建立自訂資源提供者並部署自訂資源](../custom-providers/create-custom-provider.md)
- [如何：將自訂動作新增至 Azure REST API](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [如何：將自訂資源新增至 Azure REST API](../custom-providers/custom-providers-resources-endpoint-how-to.md)
