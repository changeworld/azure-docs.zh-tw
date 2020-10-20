---
author: baanders
description: 在 Azure 數位 Twins 設定中包含步驟總覽的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205472"
---
>[!NOTE]
>這些作業的目的是要由有能力管理 Azure 訂用帳戶上資源和使用者存取權的使用者來完成。 雖然某些步驟可以使用較低的許可權來完成，但是具有這些許可權的人員必須具備這些許可權才能完全設定可用的實例。 如需詳細資訊，請參閱下面的必要條件 [*：必要許可權*](#prerequisites-permission-requirements) 一節。

新 Azure 數位 Twins 實例的完整設定包含兩個部分：
1. **建立實例**
2. **設定使用者存取權限**： azure 使用者必須在 Azure 數位 Twins 實例上擁有 *Azure 數位 Twins 擁有者 (預覽版) * 角色，才能管理它及其資料。 在此步驟中，您身為 Azure 訂用帳戶的擁有者/系統管理員會將此角色指派給即將管理 Azure 數位 Twins 實例的人員。 這可能是您自己或您組織中的其他人。