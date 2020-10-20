---
title: 使用 Azure 應用程式組態搭配 Python 應用程式的快速入門 | Microsoft Docs
description: 在本快速入門中，您會建立 Python 應用程式搭配 Azure 應用程式組態，以集中儲存和管理應用程式組態 (與您的程式碼分開)。
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 954f4edcd10d701d00d9cd23280aaac7c287992d
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997446"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式組態建立 Python 應用程式

在本快速入門中，您將使用[適用於 Python 的 Azure 應用程式組態用戶端程式庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)，利用 Azure 應用程式組態來集中儲存和管理應用程式設定。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- Python 2.7 或3.5 或更新版本 - 如需在 Windows 上設定 Python 的詳細資訊，請參閱 [Windows 上的 Python 文件]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 選取 [組態總管]   > [建立]   > [索引鍵/值]  以新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    目前先讓 [標籤]  和 [內容類型]  保持空白。

8. 選取 [套用]  。

## <a name="setting-up-the-python-app"></a>設定 Python 應用程式

1. 在本教學課程中，您將為專案建立名為 app-configuration-quickstart 的新目錄。

    ```console
    mkdir app-configuration-quickstart
    ```

1. 切換至新建立的 app-configuration-quickstart 目錄。

    ```console
    cd app-configuration-quickstart
    ```

1. 使用 `pip install` 命令安裝 Azure 應用程式組態用戶端程式庫。

    ```console
    pip install azure-appconfiguration
    ```

1. 在 app-configuration-quickstart 目錄中，建立名為 app-configuration-quickstart.py 的新檔案，並新增下列程式碼：

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> 本快速入門中的程式碼片段可協助您開始使用適用於 Python 的應用程式組態用戶端程式庫。 針對您的應用程式，您也應該考慮根據您的需求來處理例外狀況。 若要深入了解例外狀況處理，請參閱我們的 [Python SDK 文件](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)。

## <a name="configure-your-app-configuration-connection-string"></a>設定您的應用程式組態連接字串

1. 設定名為 AZURE_APP_CONFIG_CONNECTION_STRING 的環境變數，並將其設定為應用程式組態存放區的存取金鑰。 在命令列中執行下列命令：

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 macOS 或 Linux，請執行下列命令：

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. 重新啟動命令提示字元，讓變更生效。 輸出環境變數的值，以驗證其設定是否正確。

## <a name="code-samples"></a>程式碼範例

本節中的範例程式碼片段示範如何使用適用於 Python 的應用程式組態用戶端程式庫來執行一般作業。 將這些程式碼片段新增至您稍早建立的 app-configuration-quickstart.py 檔案中的 `try` 區塊。

> [!NOTE]
> 應用程式組態用戶端程式庫會將索引鍵/值物件稱作為 `ConfigurationSetting`。 因此，在本文中，應用程式組態存放區中的**索引鍵/值**會被稱作為**組態設定**。

* [連線至應用程式組態存放區](#connect-to-an-app-configuration-store)
* [取得組態設定](#get-a-configuration-setting)
* [新增組態設定](#add-a-configuration-setting)
* [取得組態設定清單](#get-a-list-of-configuration-settings)
* [鎖定組態設定](#lock-a-configuration-setting)
* [解除鎖定組態設定](#unlock-a-configuration-setting)
* [更新組態設定](#update-a-configuration-setting)
* [刪除組態設定](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>連線至應用程式組態存放區

下列程式碼片段會使用儲存在環境變數中的連接字串，建立 **AzureAppConfigurationClient** 的執行個體。

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>取得組態設定

下列程式碼片段會依 `key` 名稱來擷取組態設定。

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>新增組態設定

下列程式碼片段會建立具有 `key` 和 `value` 欄位的 `ConfigurationSetting` 物件，並叫用 `add_configuration_setting` 方法。 如果您嘗試新增您存放區中已經存在的組態設定，此方法將會擲回例外狀況。 如果您想要避免此例外狀況，可改為使用 [set_configuration_setting](#update-a-configuration-setting) 方法。

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>取得組態設定清單

下列程式碼片段會擷取組態設定清單。 您可提供 `key_filter` 和 `label_filter` 引數，以分別根據 `key` 和 `label` 來篩選索引鍵/值。 如需有關篩選的詳細資訊，請參閱如何[查詢組態設定](./concept-key-value.md#query-key-values)。

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>鎖定組態設定

應用程式組態中索引鍵/值的鎖定狀態是由 `ConfigurationSetting` 物件的 `read_only` 屬性所表示。 如果 `read_only` 為 `True`，則會鎖定此設定。 您可以使用 `read_only=True` 引數來叫用 `set_read_only` 方法，以鎖定組態設定。

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>解除鎖定組態設定

如果 `ConfigurationSetting` 的 `read_only` 屬性為 `False`，則會將此設定解除鎖定。 您可以使用 `read_only=False` 引數來叫用 `set_read_only` 方法，以將組態設定解除鎖定。

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>更新組態設定

`set_configuration_setting` 方法可用來更新現有設定，或建立新的設定。 下列程式碼片段會變更現有組態設定的值。

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>刪除組態設定

下列程式碼片段會依 `key` 名稱刪除組態設定。

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>執行應用程式

在本快速入門中，您已建立 Python 應用程式，其使用 Azure 應用程式組態用戶端程式庫來擷取透過 Azure 入口網站建立的組態設定、新增設定、擷取現有設定的清單、鎖定和解除鎖定設定、更新設定，最後刪除設定。

此時，您的 app-configuration-quickstart.py 檔案應該會有下列程式碼：

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

在主控台視窗中，瀏覽至包含 app-configuration-quickstart.py 檔案的目錄，然後執行下列 python 命令來執行應用程式：

```console
python app-configuration-quickstart.py
```

您應該會看見下列輸出：

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>清除資源


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式組態存放區，並學會如何從 Python 應用程式存取索引鍵/值。

如需其他程式碼範例，請造訪：

> [!div class="nextstepaction"]
> [Azure 應用程式組態用戶端程式庫範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)