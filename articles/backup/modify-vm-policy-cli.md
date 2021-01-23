---
title: 使用 CLI 更新現有的 VM 備份原則
description: 瞭解如何使用 Azure CLI 來更新現有的 VM 備份原則。
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728573"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>使用 CLI 更新現有的 VM 備份原則

您可以使用 Azure CLI 來更新現有的 VM 備份原則。 本文將說明如何將現有的原則匯出至 JSON 檔案、修改檔案，然後使用 Azure CLI 以修改過的原則來更新原則。

## <a name="modify-an-existing-policy"></a>修改現有的原則

若要修改現有的 VM 備份原則，請遵循下列步驟：

1. 執行 [az backup policy show](/cli/azure/backup/policy#az_backup_policy_show) 命令以取得您想要更新之原則的詳細資料。

    範例：

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    上述範例會顯示名稱為 *testing123* 之 VM 原則的詳細資料。

    輸出：

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. 將上述輸出儲存在 json 檔案中。 例如，讓我們將它儲存為 *Policy.js*。
1. 根據您的需求更新 JSON 檔案，並儲存變更。

    範例：若要將每週保留期更新為60天，請將計數變更為60，以更新 JSON 檔案的下列區段。

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. 儲存變更。
1. 執行 [az backup policy set](/cli/azure/backup/policy#az_backup_policy_set) 命令，並將更新的 JSON 檔案的完整路徑傳遞為 **--policy** 參數的值。

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>您也可以執行 [az backup policy get-default-vm](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) 命令來取得範例 JSON 原則。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 備份服務管理 Azure VM 備份](backup-azure-manage-vms.md)