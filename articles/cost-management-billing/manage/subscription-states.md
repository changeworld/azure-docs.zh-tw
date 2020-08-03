---
title: Azure 訂用帳戶狀態
description: 本文說明 Azure 訂用帳戶的不同狀態。
keywords: azure 訂用帳戶狀態
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: andalmia
ms.openlocfilehash: 0d301087578c8a944920bca932bcae3127ae50ef
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282631"
---
# <a name="azure-subscription-states"></a>Azure 訂用帳戶狀態

本文說明 Azure 訂用帳戶可能擁有的各種狀態。 在 Azure 入口網站訂用帳戶區域中，您會發現這些狀態顯示為 [狀態]。

| 訂用帳戶狀態 | 描述 |
|-------------| ----------------|
| [作用中]/[已啟用] | 您的 Azure 訂用帳戶為作用中。 您可以使用此訂用帳戶來部署新的資源及管理現有資源。<br><br>所有的作業 (PUT、PATCH、DELETE、POST、GET) 都適用於[為您的訂用帳戶註冊的](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)資源提供者。 |
| **已刪除** | 您的 Azure 訂用帳戶已連同所有基礎資源/資料一起刪除。<br><br>沒有適用的作業。 |
| **Disabled** | 您的 Azure 訂用帳戶已停用，無法再用來建立或管理 Azure 資源。 雖然處於此狀態，系統會解除配置您的虛擬機器、釋出暫時 IP 位址、儲存體會變成唯讀，而且停用其他服務。 訂用帳戶可能會因為下列原因而遭到停用：您的點數可能已到期。 您可能已達到消費限制。 您有超過期限的帳單。 已超過您的信用卡限制。 或者，其已明確停用或取消。 視訂用帳戶類型而定，訂用帳戶在 1-90 天內可能維持停用狀態。 之後，就會永久刪除。 如需詳細資訊，請參閱[重新啟用已停用的 Azure 訂用帳戶](subscription-disabled.md)。<br><br>建立或更新資源的作業 (PUT、PATCH) 會停用。 採取動作的作業 (POST) 也會停用。 您可以擷取或刪除資源 (GET、DELETE)。 您的資源仍可供使用。 |
| **已到期** | 您的 Azure 訂用帳戶已過期，因為其已遭取消。 您可以重新啟用已過期的訂用帳戶。 如需詳細資訊，請參閱[重新啟用已停用的 Azure 訂用帳戶](subscription-disabled.md)。<br><br>建立或更新資源的作業 (PUT、PATCH) 會停用。 採取動作的作業 (POST) 也會停用。 您可以擷取或刪除資源 (GET、DELETE)。|
| **超過期限** | 您的 Azure 訂用帳戶有擱置的未付款項。 您的訂用帳戶仍為作用中，但無法支付費用可能會導致訂用帳戶被停用。 如需詳細資訊，請參閱[解決 Azure 訂用帳戶的逾期未付帳款](resolve-past-due-balance.md)。<br><br>所有作業均適用。 |
| **警告** | 您的 Azure 訂用帳戶處於警告狀態，雖然可以正常使用，但是如果未解決警告原因，很快就會停用。 如果超過期限、已遭使用者取消、過期等，則訂用帳戶可能會處於警告狀態。<br><br>所有作業均適用。 |
