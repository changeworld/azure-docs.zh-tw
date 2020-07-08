---
title: 使用 REST API 建立備份原則
description: 在本文中，您將瞭解如何使用 REST API 來建立和管理備份原則（排程和保留）。
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76963847"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>使用 REST API 建立 Azure 復原服務備份原則

[原則 REST API 文件](/rest/api/backup/protectionpolicies/createorupdate)概述為 Azure 復原服務保存庫建立備份原則的步驟。 讓我們使用這份文件作為參考，以建立 Azure VM 備份的原則。

## <a name="create-or-update-a-policy"></a>建立或更新原則

若要建立或更新 Azure 備份原則，請使用下列 PUT** 作業

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

`{policyName}` 和 `{vaultName}` 都會在 URI 中提供。 要求本文中會提供其他資訊。

## <a name="create-the-request-body"></a>建立要求本文

例如，若要建立 Azure VM 備份的原則，以下是要求本文的元件。

|名稱  |必要  |類型  |Description  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy：[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource 屬性        |
|tags     |         | Object        |  資源標籤       |

如需要求本文中的完整定義清單，請參閱[備份原則 REST API 文件](/rest/api/backup/protectionpolicies/createorupdate)。

### <a name="example-request-body"></a>要求本文範例

下列要求本文會定義 Azure VM 備份的備份原則。

原則規定：

- 在每個星期一、星期三、星期四上午 10:00 (太平洋標準時間) 進行每週備份。
- 將每個星期一、星期三、星期四進行的備份保留一週。
- 將一個月當中第一個星期三和第三個星期四進行的備份保留兩個月 (覆寫先前的保留條件)。
- 將 2 月和 11 月中第四個星期一和第四個星期四進行的備份保留四年 (覆寫先前的保留條件)。

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> 排程和保留期的時間格式僅支援 DateTime。 不單獨支援 Time 格式。

## <a name="responses"></a>回應

備份原則的建立/更新為[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一個作業時，202（已接受），然後在該作業完成時傳回200（確定）。

|Name  |類型  |Description  |
|---------|---------|---------|
|200 確定     |    [保護 PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  確定       |
|202 已接受     |         |     已接受    |

### <a name="example-responses"></a>範例回應

一旦提交 PUT** 要求以供建立或更新原則，初始回應為 202 (已接受) 以及位置標頭或 Azure-async-header。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然後，使用位置標頭或 Azure-AsyncOperation 標頭搭配簡單的 *GET* 命令，來追蹤所產生的作業。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

當作業完成時，它會在回應本文中傳回 200 (確定) 以及原則內容。

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

如果原則已經用於保護項目，則任何原則更新都會導致針對所有這類相關聯的項目[修改保護](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection)。

## <a name="next-steps"></a>後續步驟

[針對未受保護的 Azure 虛擬機器啟用保護](backup-azure-arm-userestapi-backupazurevms.md)。

如需 Azure 備份 REST API 的詳細資訊，請參閱下列文件：

- [Azure 復原服務提供者 REST API](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (開始使用 Azure REST API)
