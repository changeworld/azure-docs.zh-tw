---
title: Azure API 管理中的修訂 |Microsoft Docs
description: 瞭解 Azure API 管理中修訂的概念。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: b099a6ea706482e25b2c37a87cf0a24f2fe475bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531241"
---
# <a name="revisions-in-azure-api-management"></a>Azure API 管理中的修訂

修訂可讓您以受控制且安全的方式對 Api 進行變更。 當您想要進行變更時，請建立新的修訂。 然後您可以編輯和測試 API，而不會干擾您的 API 取用者。 當您準備好時，就會將修訂設為最新版本。 同時，您也可以選擇性地將專案張貼至變更記錄檔，以讓您的 API 取用者與變更的內容保持在最新狀態。 變更記錄會發佈到您的開發人員入口網站。

> [!NOTE]
> 使用層不提供開發人員入口網站。

有了修訂，您可以：

- 安全地對您的 API 定義和原則進行變更，而不會影響您的生產 API。
- 發行之前，請先嘗試變更。
- 記錄您所做的變更，讓您的開發人員可以瞭解新功能。
- 如果您發現問題，請回復。

[遵循我們的逐步解說開始進行修訂。](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>存取特定修訂

您可以使用特殊格式的 URL 來存取 API 的每個修訂。 `;rev={revisionNumber}`在 API URL 的結尾附加，但在查詢字串之前，以存取該 API 的特定修訂。 例如，您可以使用此 URL 來存取 API 的修訂 3 `customers` ：

`https://apis.contoso.com/customers;rev=3?customerId=123`

根據預設，每個修訂的安全性設定與目前的修訂版本相同。 如果您想要針對每個修訂套用不同的安全性，您可以故意變更特定修訂的原則。 例如，您可能會想要新增[IP 允許清單原則](./api-management-access-restriction-policies.md#RestrictCallerIPs)，以防止外部呼叫端存取仍在開發中的修訂。

修訂可以離線，這讓呼叫者無法存取，即使他們嘗試透過其 URL 存取修訂也一樣。 您可以使用 Azure 入口網站，將修訂標示為離線。 如果您使用 PowerShell，您可以使用 `Set-AzApiManagementApiRevision` Cmdlet，並將 `Path` 引數設定為 `$null` 。

> [!NOTE]
> 當您未使用修訂來進行測試時，我們建議您讓修訂離線。

## <a name="current-revision"></a>目前修訂

單一修訂可以設定為*目前*的修訂。 此修訂將會用於所有未在 URL 中指定明確修訂編號的 API 要求。 您可以將修訂設定為 [目前]，以回復為先前的修訂版本。

您可以使用 Azure 入口網站，將修訂設定為目前的版本。 如果您使用 PowerShell，您可以使用 `New-AzApiManagementApiRelease` Cmdlet。

## <a name="revision-descriptions"></a>修訂描述

當您建立修訂時，您可以為自己的追蹤目的設定描述。 不會對您的 API 使用者播放描述。

當您將修訂設定為 [目前] 時，您也可以選擇性地指定公用變更記錄檔附注。 變更記錄檔包含在開發人員入口網站中，供您的 API 使用者進行查看。 您可以使用 PowerShell Cmdlet 來修改您的變更記錄附注 `Update-AzApiManagementApiRelease` 。

## <a name="versions-and-revisions"></a>版本和修訂

版本和修訂是不同的功能。 每個版本都可以有多個修訂，就像未建立版本的 API 一樣。 您可以使用未使用版本的修訂，或另一種方式。 通常版本是用來將 API 版本與重大變更分開，而修訂可以用於 API 的次要和非重大變更。

如果您的修訂有重大變更，或您想要正式地將修訂轉換成 Beta/test 版本，您可以從修訂建立版本。 使用 Azure 入口網站，按一下 [修訂] 索引標籤上 [修訂] 內容功能表上的 [從修訂建立版本]。
