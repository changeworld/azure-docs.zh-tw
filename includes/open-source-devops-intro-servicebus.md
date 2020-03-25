---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67174669"
---
[Azure 服務匯流排](/azure/service-bus-messaging/service-bus-messaging-overview)是企業[整合](https://azure.microsoft.com/product-categories/integration/)訊息代理程式。 服務匯流排支援兩種通訊類型：佇列和主題。 

佇列支援應用程式之間的非同步通訊。 應用程式會將訊息傳送至佇列，以儲存訊息。 接收端應用程式則會與佇列連線並從中讀取訊息。

主題支援「發佈-訂閱」模式，可在訊息建立者和訊息接收者之間實現一對多關聯性。