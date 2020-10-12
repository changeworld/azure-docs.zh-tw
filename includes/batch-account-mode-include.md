---
title: 包含檔案
description: 包含檔案
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 51b687dc2a6264eb12362616429746c9b182c3b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68323897"
---
> [!NOTE]
> 建立 Batch 帳戶時，您可以選擇兩種「集區配置」** 模式：[使用者訂用帳戶]**** 和 [Batch 服務]****。 在大部分情況下，您應該使用預設 Batch 服務模式，以在幕後將集區配置在 Azure 管理的訂用帳戶中。 在其他使用者訂用帳戶模式中，在建立集區時，會直接在您的訂用帳戶中建立 Batch VM 和其他資源。 如果您想要使用 [Azure 保留的 VM 執行個體](https://azure.microsoft.com/pricing/reserved-vm-instances/)建立 Batch 集區，則需要使用者訂用帳戶模式。 若要在使用者訂用帳戶模式中建立 Batch 帳戶，您也必須向 Azure Batch 註冊訂用帳戶，並與 Azure Key Vault 中的帳戶建立關聯。