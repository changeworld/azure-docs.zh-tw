---
title: Azure 訂用帳戶狀態
description: 說明 Azure 訂用帳戶的不同狀態
keywords: azure 訂用帳戶狀態
author: anuragdalmia
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: andalmia
ms.openlocfilehash: df8a60c0249eb51168e1a67cdd67116813312626
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200532"
---
# <a name="azure-subscription-states"></a>Azure 訂用帳戶狀態
本文說明 Azure 訂用帳戶可能擁有的各種狀態。 在 [訂用帳戶] 刀鋒視窗底下，您會發現這些狀態顯示為「狀態」。

| 訂用帳戶狀態 | 描述 |
|-------------| ----------------|
| **使用中** | 您的 Azure 訂用帳戶為作用中。 您可以使用此訂用帳戶來部署新的資源及管理現有的資源。|
| **超過期限** | 您的 Azure 訂用帳戶有擱置的未付款項。 您的訂用帳戶仍為作用中，但無法支付費用可能會導致訂用帳戶被停用。 [解決 Azure 訂用帳戶的逾期未付帳款。](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Disabled** | 您的 Azure 訂用帳戶已停用，無法再用來建立或管理 Azure 資源。 雖然處於此狀態，系統會解除配置您的虛擬機器、釋出暫時 IP 位址、儲存體會變成唯讀，而且停用其他服務。 訂用帳戶可以停用，因為您的點數可能已到期、您可能已達消費限制、您有逾期未繳帳單、您的信用卡限制已超過，或您已將其明確停用/取消。 視訂用帳戶類型和停用原因而定，訂用帳戶在 1-90 天之間可能會保持停用狀態，之後就會永久刪除。 [重新啟用已停用的 Azure 訂用帳戶。](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **已刪除** | 您的 Azure 訂用帳戶已連同所有基礎資源/資料一起刪除。 |
