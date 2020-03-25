---
title: Azure 訂用帳戶狀態
description: 本文說明 Azure 訂用帳戶的不同狀態。
keywords: azure 訂用帳戶狀態
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78270226"
---
# <a name="azure-subscription-states"></a>Azure 訂用帳戶狀態

本文說明 Azure 訂用帳戶可能擁有的各種狀態。 在 Azure 入口網站訂用帳戶區域中，您會發現這些狀態顯示為 [狀態]  。

| 訂用帳戶狀態 | 描述 |
|-------------| ----------------|
| **使用中** | 您的 Azure 訂用帳戶為作用中。 您可以使用此訂用帳戶來部署新的資源及管理現有資源。|
| **已刪除** | 您的 Azure 訂用帳戶已連同所有基礎資源/資料一起刪除。 |
| **Disabled** | 您的 Azure 訂用帳戶已停用，無法再用來建立或管理 Azure 資源。 雖然處於此狀態，系統會解除配置您的虛擬機器、釋出暫時 IP 位址、儲存體會變成唯讀，而且停用其他服務。 訂用帳戶可能會因為下列原因而遭到停用：您的點數可能已到期。 您可能已達到消費限制。 您有超過期限的帳單。 已超過您的信用卡限制。 或者，其已明確停用或取消。 視訂用帳戶類型而定，訂用帳戶在 1-90 天內可能維持停用狀態。 之後，就會永久刪除。 如需詳細資訊，請參閱[重新啟用已停用的 Azure 訂用帳戶](subscription-disabled.md)。 |
| **已到期** | 您的 Azure 訂用帳戶已過期，因為其已遭取消。 您可以重新啟用已過期的訂用帳戶。 如需詳細資訊，請參閱[重新啟用已停用的 Azure 訂用帳戶](subscription-disabled.md)。|
| **超過期限** | 您的 Azure 訂用帳戶有擱置的未付款項。 您的訂用帳戶仍為作用中，但無法支付費用可能會導致訂用帳戶被停用。 如需詳細資訊，請參閱[解決 Azure 訂用帳戶的逾期未付帳款](resolve-past-due-balance.md)。 |
