---
title: 以程式設計方式建立 Azure 訂用帳戶
description: 本文將協助您了解以程式設計方式建立 Azure 訂用帳戶的選項。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a51a158700df52ced5288cf2c2f82ebcdf3de54d
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254010"
---
# <a name="create-azure-subscriptions-programatically"></a>以程式設計方式建立 Azure 訂用帳戶

本文將協助您了解以程式設計方式建立 Azure 訂用帳戶的選項。

藉由使用各種 REST API，您可以為下列 Azure 合約類型建立訂用帳戶：

- Enterprise 合約 (EA)
- Microsoft 客戶合約 (MCA)
- Microsoft 合作夥伴合約 (MPA)

您無法透過 REST API 以程式設計方式為其他合約類型建立其他訂用帳戶。

建立訂用帳戶的需求和詳細資料會因不同合約和 API 版本而有所不同。 請參閱下列適用於您情況的文章：

最新的 API：

- [建立 EA 訂用帳戶](programmatically-create-subscription-enterprise-agreement.md)
- [建立 MCA 訂用帳戶](programmatically-create-subscription-microsoft-customer-agreement.md)
- [建立 MPA 訂用帳戶](programmatically-create-subscription-microsoft-partner-agreement.md)

如果您仍在使用[預覽 API](programmatically-create-subscription-preview.md)，您可以繼續使用這些 API 來建立訂用帳戶。 

而且，您可以[透過 ARM 範本建立訂用帳戶](create-subscription-template.md)。 ARM 範本可協助您使用 REST API 來自動化訂用帳戶建立程序。 

## <a name="next-steps"></a>後續步驟

* 建立訂用帳戶之後，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)。
