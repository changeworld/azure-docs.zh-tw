---
title: Azure RTOS 安全性模組 API
description: 適用于 Azure RTO 之安全性模組的參考 API。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 3aeab8827ad030df1a1ea757956285678ec46ef6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514834"
---
# <a name="security-module-for-azure-rtos-api-preview"></a>適用于 Azure RTO API (preview 的安全性模組) 

此 API 僅供適用于 Azure RTO 的安全性模組使用。 如需其他資源，請參閱 [AZURE Rto GitHub 資源的安全性模組](https://github.com/azure-rtos/azure-iot-preview/releases)。 

## <a name="enable-security-module-for-azure-rtos"></a>啟用 Azure RTO 的安全性模組

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>原型

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>描述

此常式會啟用 Azure IoT 安全性模組子系統。 內部狀態機器會管理安全性事件的集合，並將其傳送至 Azure IoT 中樞。 管理資料收集時，只需要一個 NX_AZURE_IOT_SECURITY_MODULE 實例。

### <a name="parameters"></a>參數

| 名稱 | 描述 |
|---------|---------|
| nx_azure_iot_ptr [in]    | `NX_AZURE_IOT` 的指標。  |

### <a name="return-values"></a>傳回值

|傳回值  |描述 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   已成功啟用 Azure IoT 安全性模組。     |
|NX_AZURE_IOT_FAILURE   |  因為發生內部錯誤，所以無法啟用 Azure IoT 安全性模組。    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  安全性模組需要有效的 #NX_AZURE_IOT 實例。      |

### <a name="allowed-from"></a>允許來源

Threads

## <a name="disable-azure-iot-security-module"></a>停用 Azure IoT 安全性模組

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>原型

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>描述

此常式會停用 Azure IoT 安全性模組子系統。

### <a name="parameters"></a>參數

| 名稱 | 描述 |
|---------|---------|
| nx_azure_iot_ptr [in]    | `NX_AZURE_IOT` 的指標。 如果為 Null，則會停用 singleton 實例。 |

### <a name="return-values"></a>傳回值

|傳回值  |描述 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   成功停用 Azure IoT 安全性模組時成功。      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT 中樞實例與單一複合實例不同。       |
|NX_AZURE_IOT_FAILURE    |  因為發生內部錯誤，所以無法停用 Azure IoT 安全性模組。       |

### <a name="allowed-from"></a>允許來源

Threads


## <a name="next-steps"></a>接下來的步驟

若要深入瞭解如何開始使用 Azure RTO 安全性模組，請參閱下列文章：

- 請參閱適用於 IoT 的 Azure 資訊安全中心 RTO 安全性模組 [總覽](iot-security-azure-rtos.md)。