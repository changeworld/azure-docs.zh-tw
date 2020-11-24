---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 1485adc1407720343c27e07c2d2f4a13ba037a90
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511427"
---
## <a name="model-id-announcement"></a>模型識別碼宣告

若要宣告模型識別碼，裝置必須將它包含在連接資訊中：

```c
static const char g_ThermostatModelId[] = "dtmi:com:example:Thermostat;1";
IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceHandle = NULL;
deviceHandle = CreateDeviceClientLLHandle();
iothubResult = IoTHubDeviceClient_LL_SetOption(
    deviceHandle, OPTION_MODEL_ID, g_ThermostatModelId);
```

> [!TIP]
> 針對模組和 IoT Edge，請使用 `IoTHubModuleClient_LL` 取代 `IoTHubDeviceClient_LL` 。

## <a name="dps-payload"></a>DPS 承載

使用「裝置布建 [服務」 (DPS) ](../articles/iot-dps/about-iot-dps.md) 的裝置可以包含在布建程式 `modelId` 期間使用下列 JSON 承載所要使用的。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>執行遙測、屬性和命令

如 [瞭解 IoT 隨插即用模型中的元件](../articles/iot-pnp/concepts-components.md)所述，裝置構建者必須決定是否要使用元件來描述其裝置。 使用元件時，裝置必須遵循本節所述的規則。

### <a name="telemetry"></a>遙測

預設元件不需要任何特殊屬性。

使用嵌套元件時，裝置必須設定具有元件名稱的訊息屬性：

```c
void PnP_ThermostatComponent_SendTelemetry(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    IOTHUB_CLIENT_RESULT iothubResult;

    char temperatureStringBuffer[32];

    if (snprintf(
        temperatureStringBuffer,
        sizeof(temperatureStringBuffer),
        g_temperatureTelemetryBodyFormat,
        pnpThermostatComponent->currentTemperature) < 0)
    {
        LogError("snprintf of current temperature telemetry failed");
    }
    else if ((messageHandle = PnP_CreateTelemetryMessageHandle(
        pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
    {
        LogError("Unable to create telemetry message");
    }
    else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(
        deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send telemetry message, error=%d", iothubResult);
    }

    IoTHubMessage_Destroy(messageHandle);
}

// ...

PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
```

### <a name="read-only-properties"></a>唯讀屬性

從預設元件報告屬性不需要任何特殊的結構：

```c
static const char g_maxTemperatureSinceRebootFormat[] = "{\"maxTempSinceLastReboot\":%.2f}";

char maxTemperatureSinceRebootProperty[256];

snprintf(
    maxTemperatureSinceRebootProperty,
    sizeof(maxTemperatureSinceRebootProperty),
    g_maxTemperatureSinceRebootFormat,
    38.7);

IOTHUB_CLIENT_RESULT iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)maxTemperatureSinceRebootProperty,
    strlen(maxTemperatureSinceRebootProperty), NULL, NULL));
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

使用嵌套元件時，必須在元件名稱內建立屬性：

```c
STRING_HANDLE PnP_CreateReportedProperty(
    const char* componentName,
    const char* propertyName,
    const char* propertyValue
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":%s}",
            propertyName, propertyValue);
    }
    else 
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":%s}}",
            componentName, propertyName, propertyValue);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent =
        (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    char maximumTemperatureAsString[32];
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;

    if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString),
        "%.2f", pnpThermostatComponent->maxTemperature) < 0)
    {
        LogError("Unable to create max temp since last reboot string for reporting result");
    }
    else if ((jsonToSend = PnP_CreateReportedProperty(
                pnpThermostatComponent->componentName,
                g_maxTempSinceLastRebootPropertyName,
                maximumTemperatureAsString)) == NULL)
    {
        LogError("Unable to build max temp since last reboot property");
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
                deviceClientLL,
                (const unsigned char*)jsonToSendStr,
                jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
        {
            LogError("Unable to send reported state, error=%d", iothubClientResult);
        }
        else
        {
            LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s",
                pnpThermostatComponent->componentName);
        }
    }

    STRING_delete(jsonToSend);
}

// ...

PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>可寫入屬性

這些屬性可以由裝置設定，或由解決方案進行更新。 如果解決方案更新屬性，用戶端會收到通知作為或中的回呼 `DeviceClient` `ModuleClient` 。 若要遵循 IoT 隨插即用慣例，裝置必須通知服務已成功接收屬性。

#### <a name="report-a-writable-property"></a>報告可寫入的屬性

當裝置報告可寫入的屬性時，它必須包含 `ack` 慣例中定義的值。

從預設元件報告可寫入的屬性：

```c
IOTHUB_CLIENT_RESULT iothubClientResult;
char targetTemperatureResponseProperty[256];

snprintf(
    targetTemperatureResponseProperty,
    sizeof(targetTemperatureResponseProperty),
    "{\"targetTemperature\":{\"value\":%.2f,\"ac\":%d,\"av\":%d,\"ad\":\"%s\"}}",
    23.2, 200, 3, "Successfully updated target temperature");

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)targetTemperatureResponseProperty,
    strlen(targetTemperatureResponseProperty), NULL, NULL);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

若要從嵌套的元件報告可寫入的屬性，對應項必須包含標記：

```c
STRING_HANDLE PnP_CreateReportedPropertyWithStatus(const char* componentName,
    const char* propertyName, const char* propertyValue,
    int result, const char* description, int ackVersion
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}",
            propertyName, propertyValue,
            result, description, ackVersion);
    }
    else
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}}",
            componentName, propertyName, propertyValue,
            result, description, ackVersion);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

// ...

char targetTemperatureAsString[32];
IOTHUB_CLIENT_RESULT iothubClientResult;
STRING_HANDLE jsonToSend = NULL;

snprintf(targetTemperatureAsString,
    sizeof(targetTemperatureAsString),
    "%.2f",
    23.2);
jsonToSend = PnP_CreateReportedPropertyWithStatus(
    "thermostat1",
    "targetTemperature",
    targetTemperatureAsString,
    200,
    "complete",
    3);

const char* jsonToSendStr = STRING_c_str(jsonToSend);
size_t jsonToSendStrLen = strlen(jsonToSendStr);

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)jsonToSendStr,
    jsonToSendStrLen, NULL, NULL);

STRING_delete(jsonToSend);
```

裝置對應項會以下一個回報的屬性更新：

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>訂閱所需的屬性更新

服務可以更新所需的屬性，這些屬性會在連線的裝置上觸發通知。 此通知包含已更新的所需屬性，包括識別更新的版本號碼。 裝置必須使用與 `ack` 報告屬性相同的訊息來回應。

預設元件會看到單一屬性，並 `ack` 使用收到的版本建立報告：

```c
static void Thermostat_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback)
{
    // The device handle associated with this request is passed as the context,
    // since we will need to send reported events back.
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL =
        (IOTHUB_DEVICE_CLIENT_LL_HANDLE)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    JSON_Value* versionValue = NULL;
    JSON_Value* targetTemperatureValue = NULL;

    jsonStr = CopyTwinPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    targetTemperatureValue = json_object_get_value(desiredObject, "targetTemperature"));
    versionValue = json_object_get_value(desiredObject, "$version"));
    json_value_get_type(versionValue);
    json_value_get_type(targetTemperatureValue);

    double targetTemperature = json_value_get_number(targetTemperatureValue);
    int version = (int)json_value_get_number(versionValue);

    // ...

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureReport(deviceClientLL, targetTemperature, 200, version, "Successfully updated target temperature");

    json_value_free(rootValue);
    free(jsonStr);
}

// ...

IOTHUB_CLIENT_RESULT iothubResult;
iothubResult = IoTHubDeviceClient_LL_SetDeviceTwinCallback(
    deviceHandle, Thermostat_DeviceTwinCallback, (void*)deviceHandle))
```

裝置對應項會在所需和報告的區段中顯示內容：

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

嵌套的元件會接收以元件名稱包裝的所需屬性，且應該回報 `ack` 報告的屬性：

```c
bool PnP_ProcessTwinData(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size, const char** componentsInModel,
    size_t numComponentsInModel,
    PnP_PropertyCallbackFunction pnpPropertyCallback,
    void* userContextCallback)
{
    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    bool result;

    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    
    result = VisitDesiredObject(
        desiredObject, componentsInModel,
        numComponentsInModel, pnpPropertyCallback,
        userContextCallback);


    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...
static const char g_thermostatComponent1Name[] = "thermostat1";
static const size_t g_thermostatComponent1Size = sizeof(g_thermostatComponent1Name) - 1;
static const char g_thermostatComponent2Name[] = "thermostat2";

static const char* g_modeledComponents[] = {g_thermostatComponent1Name, g_thermostatComponent2Name};
static const size_t g_numModeledComponents = sizeof(g_modeledComponents) / sizeof(g_modeledComponents[0]);

static void PnP_TempControlComponent_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback
)
{
    PnP_ProcessTwinData(
        updateState, payload,
        size, g_modeledComponents,
        g_numModeledComponents,
        PnP_TempControlComponent_ApplicationPropertyCallback,
        userContextCallback);
}
```

嵌套元件的裝置對應項會顯示所需和所報告的區段，如下所示：

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>命令

預設元件會收到服務所叫用的命令名稱。

嵌套的元件會收到前面加上元件名稱和分隔符號的命令名稱 `*` 。

```c
void PnP_ParseCommandName(
    const char* deviceMethodName,
    unsigned const char** componentName,
    size_t* componentNameSize,
    const char** pnpCommandName
)
{
    const char* separator;

    if ((separator = strchr(deviceMethodName, "*")) != NULL)
    {
        *componentName = (unsigned const char*)deviceMethodName;
        *componentNameSize = separator - deviceMethodName;
        *pnpCommandName = separator + 1;
    }
    else
    {
        *componentName = NULL;
        *componentNameSize = 0;
        *pnpCommandName = deviceMethodName;
    }
}

static int PnP_TempControlComponent_DeviceMethodCallback(
    const char* methodName,
    const unsigned char* payload,
    size_t size,
    unsigned char** response,
    size_t* responseSize,
    void* userContextCallback)
{
    (void)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    int result;
    unsigned const char *componentName;
    size_t componentNameSize;
    const char *pnpCommandName;

    *response = NULL;
    *responseSize = 0;

    // Parse the methodName into its componentName and CommandName.
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);

    // Parse the JSON of the payload request.
    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    if (componentName != NULL)
    {
        if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
        }
        else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
        }
        else
        {
            LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }
    else
    {
        LogInfo("Received PnP command for TemperatureController component, command=%s", pnpCommandName);
        if (strcmp(pnpCommandName, g_rebootCommand) == 0)
        {
            result = PnP_TempControlComponent_InvokeRebootCommand(rootValue);
        }
        else
        {
            LogError("PnP command=s%s is not supported by TemperatureController", pnpCommandName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }

    if (*response == NULL)
    {
        SetEmptyCommandResponse(response, responseSize, &result);
    }

    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...

PNP_DEVICE_CONFIGURATION g_pnpDeviceConfiguration;
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

#### <a name="request-and-response-payloads"></a>要求和回應承載

命令使用類型來定義其要求和回應裝載。 裝置必須還原序列化傳入輸入參數，並將回應序列化。 下列範例顯示如何使用裝載中定義的複雜類型來執行命令：

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

下列程式碼片段顯示裝置如何執行此命令定義，包括用來啟用序列化和還原序列化的類型：

```c
static const char g_maxMinCommandResponseFormat[] = "{\"maxTemp\":%.2f,\"minTemp\":%.2f,\"avgTemp\":%.2f,\"startTime\":\"%s\",\"endTime\":\"%s\"}";

// ...

static bool BuildMaxMinCommandResponse(
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent,
    unsigned char** response,
    size_t* responseSize)
{
    int responseBuilderSize = 0;
    unsigned char* responseBuilder = NULL;
    bool result;
    char currentTime[TIME_BUFFER_SIZE];

    BuildUtcTimeFromCurrentTime(currentTime, sizeof(currentTime));
    responseBuilderSize = snprintf(NULL, 0, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures /
        pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime, currentTime));

    responseBuilder = calloc(1, responseBuilderSize + 1));

    responseBuilderSize = snprintf(
        (char*)responseBuilder, responseBuilderSize + 1, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures / pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime,
        currentTime));

    *response = responseBuilder;
    *responseSize = (size_t)responseBuilderSize;

    return true;
}
```

> [!Tip]
> 要求和回應名稱不會出現在透過網路傳輸的序列化承載中。
