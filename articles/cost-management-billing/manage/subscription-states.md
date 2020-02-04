---
title: Azure 訂用帳戶狀態
description: 說明 Azure 訂用帳戶的不同狀態
keywords: azure 訂用帳戶狀態
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 741e7cf0869e36b5788d0a883a4e982caf041512
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995932"
---
# <a name="azure-subscription-states"></a>Azure 訂用帳戶狀態
本文說明 Azure 訂用帳戶可能擁有的各種狀態。 在 [訂用帳戶] 刀鋒視窗底下，您會發現這些狀態顯示為「狀態」。

| 訂用帳戶狀態 | 描述 |
|-------------| ----------------|
| **使用中** | 您的 Azure 訂用帳戶為作用中。 您可以使用此訂用帳戶來部署新的資源及管理現有的資源。|
| **超過期限** | 您的 Azure 訂用帳戶有擱置的未付款項。 您的訂用帳戶仍為作用中，但無法支付費用可能會導致訂用帳戶被停用。 [解決 Azure 訂用帳戶的逾期未付帳款。](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Disabled** | 您的 Azure 訂用帳戶已停用，無法再用來建立或管理 Azure 資源。 雖然處於此狀態，系統會解除配置您的虛擬機器、釋出暫時 IP 位址、儲存體會變成唯讀，而且停用其他服務。 訂用帳戶可以停用，因為您的點數可能已到期、您可能已達消費限制、您有逾期未繳帳單、您的信用卡限制已超過，或您已將其明確停用/取消。 視訂用帳戶類型和停用原因而定，訂用帳戶在 1-90 天之間可能會保持停用狀態，之後就會永久刪除。 [重新啟用已停用的 Azure 訂用帳戶。](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **已刪除** | 您的 Azure 訂用帳戶已連同所有基礎資源/資料一起刪除。 |
