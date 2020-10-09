---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174640"
---
DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。

例如，網域 'contoso.com' 可能包含數筆 DNS 記錄，例如 'mail.contoso.com' (用於郵件伺服器) 和 'www.contoso.com' (用於網站)。

在 Azure DNS 中建立 DNS 區域時︰

* 區域的名稱在資源群組內必須是唯一的，且區域必須尚未存在。 否則作業會失敗。
* 不同的資源群組或不同的 Azure 訂用帳戶中，可重複使用相同的區域名稱。
* 多個區域共用相同的名稱時，系統就會將不同的名稱伺服器位址，指派給每個執行個體。 只可以向網域名稱註冊機構登記一組位址。

> [!NOTE]
> 您不必擁有網域名稱，也能在 Azure DNS 中以該網域名稱建立 DNS 區域。 不過，的確需要擁有網域，才能將 Azure DNS 名稱伺服器，設定為網域名稱的正確名稱伺服器，供網域名稱註冊機構使用。
> 
> 如需詳細資訊，請參閱[將網域委派給 Azure DNS](../articles/dns/dns-domain-delegation.md)。