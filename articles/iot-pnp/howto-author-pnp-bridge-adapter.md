---
title: 如何建立 IoT 隨插即用橋接器的介面卡 |Microsoft Docs
description: 識別 IoT 隨插即用 bridge 介面卡元件。 瞭解如何藉由撰寫您自己的介面卡來擴充橋接器。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746819"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>擴充 IoT 隨插即用橋接器
[IoT 隨插即用橋接器](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture)可讓您將連接至閘道的現有裝置連線到 IoT 中樞。 您可以使用橋接器將 IoT 隨插即用介面對應到連接的裝置。 IoT 隨插即用介面會定義裝置所傳送的遙測、裝置與雲端之間的屬性同步，以及裝置所回應的命令。 您可以在 Windows 或 Linux 閘道上安裝和設定開放原始碼橋接器應用程式。 此外，橋接器也可以 Azure IoT Edge 執行時間模組的形式來執行。

本文詳細說明如何：

- 使用介面卡擴充 IoT 隨插即用橋接器。
- 針對橋接器介面卡執行常見的回呼。

如需示範如何使用橋接器的簡單範例，請參閱 [如何將在 Linux 或 Windows 上執行的 IoT 隨插即用 bridge 範例連線到 IoT 中樞](howto-use-iot-pnp-bridge.md)。

本文中的指引和範例假設您對 [Azure 數位 Twins](../digital-twins/overview.md) 和 [IoT 隨插即用](overview-iot-plug-and-play.md)有基本的熟悉度。 此外，本文也假設您已熟悉如何 [建立和部署 IoT 隨插即用橋接器](howto-build-deploy-extend-pnp-bridge.md)。

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>使用介面卡擴充 IoT 隨插即用橋接器的設計指南

若要擴充橋接器的功能，您可以撰寫自己的橋接器介面卡。

橋接器使用介面卡來：

- 建立裝置與雲端之間的連接。
- 啟用裝置與雲端之間的資料流程。
- 從雲端啟用裝置管理。

每個橋接器介面卡都必須：

- 建立數位 twins 介面。
- 使用介面將裝置端功能系結至雲端式功能，例如遙測、屬性和命令。
- 使用裝置硬體或固件來建立控制和資料通訊。

每個橋接器介面卡都會根據介面卡連線到裝置的方式與裝置互動，與特定類型的裝置互動。 即使與裝置的通訊使用信號交換通訊協定，橋接器介面卡還是可以有多種方式來解讀裝置中的資料。 在此案例中，橋接器介面卡會使用設定檔案中的介面卡資訊，來判斷介面卡應用來剖析資料的 *介面* 設定。

若要與裝置互動，橋接器介面卡會使用裝置所支援的通訊協定，以及基礎作業系統或裝置廠商所提供的 Api。

為了與雲端互動，橋接器介面卡會使用 Azure IoT 裝置 C SDK 提供的 Api 來傳送遙測、建立數位對應項介面、傳送屬性更新，以及建立屬性更新和命令的回呼函數。

### <a name="create-a-bridge-adapter"></a>建立橋接器介面卡

橋接器需要橋接器介面卡來執行 [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) 介面中定義的 api：

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

在此介面中：

- `PNPBRIDGE_ADAPTER_CREATE` 建立介面卡，並設定介面管理資源。 介面卡也可能依賴全域介面卡參數來建立介面卡。 此函式會針對單一介面卡呼叫一次。
- `PNPBRIDGE_COMPONENT_CREATE` 建立數位對應項用戶端介面，並系結回呼函數。 介面卡會起始裝置的通道。 介面卡可設定資源以啟用遙測流程，但在呼叫之前，不會開始報告遙測 `PNPBRIDGE_COMPONENT_START` 。 此函式會針對設定檔中的每個介面元件呼叫一次。
- `PNPBRIDGE_COMPONENT_START` 呼叫以讓橋接器介面卡開始將遙測從裝置轉送到數位對應項用戶端。 此函式會針對設定檔中的每個介面元件呼叫一次。
- `PNPBRIDGE_COMPONENT_STOP` 停止遙測流程。
- `PNPBRIDGE_COMPONENT_DESTROY` 終結數位對應項用戶端和相關聯的介面資源。 當橋接器中斷或發生嚴重錯誤時，設定檔中的每個介面元件都會呼叫此函式一次。
- `PNPBRIDGE_ADAPTER_DESTROY` 清除橋接器介面卡資源。

### <a name="bridge-core-interaction-with-bridge-adapters"></a>使用橋接器介面卡橋接核心互動

下列清單概述橋接器啟動時會發生什麼事：

1. 橋接器開始時，橋接器介面卡管理員會查看設定檔中定義的每個介面元件，並 `PNPBRIDGE_ADAPTER_CREATE` 在適當的介面卡上呼叫。 介面卡可能會使用全域介面卡設定參數來設定資源，以支援各種 *介面* 設定。
1. 針對設定檔中的每個裝置，橋接器管理員會 `PNPBRIDGE_COMPONENT_CREATE` 在適當的橋接器介面卡中呼叫，以起始介面建立。
1. 介面卡會接收介面元件的任何選用介面卡設定，並使用此資訊來設定裝置的連線。
1. 介面卡會建立數位對應項用戶端介面，並系結屬性更新和命令的回呼函數。 建立裝置連線時，不應該在數位對應項介面建立成功之後封鎖回呼的回呼。 作用中的裝置連線與橋接器所建立的 active interface 用戶端無關。 如果連接失敗，介面卡會假設裝置處於非使用中狀態。 橋接器介面卡可以選擇重試進行此連接。
1. 在橋接器介面卡管理員建立設定檔中指定的所有介面元件之後，它會向 Azure IoT 中樞註冊所有介面。 註冊是封鎖的非同步呼叫。 當呼叫完成時，它會觸發橋接器介面卡中的回呼，然後開始處理雲端中的屬性和命令回呼。
1. 橋接器介面卡管理員接著會 `PNPBRIDGE_INTERFACE_START` 在每個元件上呼叫，且橋接器介面卡會開始向數位對應項用戶端報告遙測。

### <a name="design-guidelines"></a>設計指導方針

當您開發新的橋接器介面卡時，請遵循下列指導方針：

- 判斷支援哪些裝置功能，以及使用此介面卡的元件介面定義看起來會像這樣。
- 判斷介面卡需要在設定檔中定義的介面和全域參數。
- 識別支援元件屬性和命令所需的低層級裝置通訊。
- 判斷介面卡如何剖析裝置中的原始資料，並將它轉換成 IoT 隨插即用介面定義所指定的遙測類型。
- 執行先前所述的橋接器介面卡介面。
- 將新的介面卡新增至介面卡資訊清單，並建立橋接器。

### <a name="enable-a-new-bridge-adapter"></a>啟用新的橋接器介面卡

您可以在 [adapter_manifest](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)中新增參考，藉以啟用橋接器中的介面卡：

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> 系統會依序叫用橋接器介面卡回呼。 介面卡不應封鎖回呼，因為這可防止橋接器核心進行進度。

### <a name="sample-camera-adapter"></a>範例攝影機介面卡

[攝影機介面卡讀我檔案](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md)說明您可以啟用的範例攝影機介面卡。

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>常見介面卡案例/回呼的程式碼範例

下一節將提供有關橋接器的介面卡如何針對一些常見案例來執行回呼的詳細資料，本節涵蓋下列回呼：
- [ (雲端至裝置) 接收屬性更新 ](#receive-property-update-cloud-to-device)
- [將屬性更新 (裝置回報給雲端) ](#report-a-property-update-device-to-cloud)
- [將遙測 (裝置傳送到雲端) ](#send-telemetry-device-to-cloud)
- [從雲端接收命令更新回呼，並在裝置端將其處理 (雲端至裝置) ](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [將裝置端 (裝置上的命令更新回應至雲端) ](#respond-to-command-update-on-the-device-side-device-to-cloud)

下列範例是以 [環境感應器範例介面卡](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor)為基礎。

### <a name="receive-property-update-cloud-to-device"></a> (雲端至裝置) 接收屬性更新
第一個步驟是註冊回呼函數：

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
下一步是要執行回呼函式，以讀取裝置上的屬性更新：

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>將屬性更新 (裝置回報給雲端) 
在建立元件之後的任何時間點，您的裝置都可以將屬性回報給雲端，狀態如下： 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>將遙測 (裝置傳送到雲端) 
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>從雲端接收命令更新回呼，並在裝置端將其處理 (雲端至裝置) 
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>將裝置端 (裝置上的命令更新回應至雲端) 

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解 IoT 隨插即用橋接器，請造訪 [IoT 隨插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存放庫。
