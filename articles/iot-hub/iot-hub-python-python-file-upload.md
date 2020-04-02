---
title: 將裝置中的檔案上傳至使用 Python 的 Azure IoT 中樞 | Microsoft Docs
description: 如何使用適用於 Python 的 Azure IoT 裝置 SDK 將檔案從裝置上傳至雲端。 上傳的檔案會儲存在 Azure 儲存體 blob 容器中。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.openlocfilehash: 706e1920c6c4fe39e885fd3f5a631070545509ee
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529242"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>使用 IoT 中心 (Python) 將檔案從裝置上載到雲端

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本文說明如何使用 [IoT 中樞的檔案上傳功能](iot-hub-devguide-file-upload.md)，將檔案上傳到 [Azure blob 儲存體](../storage/index.yml)。 本教學課程說明如何：

* 安全地提供儲存體容器來上傳檔案。

* 使用 Python 用戶端透過 IoT 中樞上傳檔案。

將[遙測數據從設備發送到 IoT 中心](quickstart-send-telemetry-python.md)快速入門演示了 IoT 中心的基本設備到雲端訊息傳遞功能。 不過，在某些情況下，您無法輕易地將裝置傳送的資料對應到 IoT 中樞接受且相對較小的裝置到雲端訊息。 當您需要從裝置上傳檔案時，您仍然可以使用安全可靠的 IoT 中樞。

在本教學結束時,您將執行 Python 主控台應用:

* **FileUpload.py**，其會使用 Python 裝置 SDK 將檔案上傳至儲存體。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定,該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>從裝置應用程式上傳檔案

在本節中，您可以建立裝置應用程式來將檔案上傳到 IoT 中樞。

1. 在命令提示符下,執行以下命令以安裝**azure-iot 設備**包。 使用此包可以協調與IoT中心的檔上載。

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 在命令提示符下,執行以下命令以安裝[**azure.storage.blob**](https://pypi.org/project/azure-storage-blob/)包。 使用此包執行檔上載。

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. 建立要上載到 Blob 儲存的測試檔。

1. 使用文字編輯器，在工作資料夾中建立 **FileUpload.py** 檔案。

1. 在 **FileUpload.py** 檔案開頭處新增下列 `import` 陳述式和變數。

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. 在您的檔案中，將 `[Device Connection String]` 更換為您 IoT 中樞裝置的連接字串。 替換為`[Full path to local file]`您建立的測試檔的路徑或要上傳的任何裝置上的任何檔。

1. 建立檔案載入 blob 儲存的函數:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    此函數解析傳遞給*它blob_info*結構以創建用於初始化[azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python)的 URL。 然後,它使用此用戶端將檔上載到 Azure Blob 儲存。

1. 新增以下代碼以連線客戶端並上傳檔案:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    此代碼建立非**同步 IoTHubDeviceClient,** 並使用以下 API 使用 IoT 中心管理檔上載:

    * **get_storage_info_for_blob**從IoT中心獲取有關您以前創建的連結存儲帳戶的資訊。 此資訊包括主機名、容器名稱、blob 名稱和 SAS 令牌。 儲存資訊將傳遞到**store_blob**函數(在上一步中創建),因此該函數中的**BlobClient**可以使用 Azure 儲存進行身份驗證。 **get_storage_info_for_blob**方法還返回correlation_id,該correlation_id在**notify_blob_upload_status**方法中使用。 correlation_id是 IoT 中心標記您正在處理的 Blob 的方法。

    * **notify_blob_upload_status**通知 IoT 中心 blob 儲存操作的狀態。 通過**get_storage_info_for_blob**方法獲取correlation_id。 IoT 中心使用它來通知任何可能正在偵聽檔上傳任務狀態通知的服務。

1. 儲存並關閉 **UploadFile.py** 檔案。

## <a name="run-the-application"></a>執行應用程式

現在,您已準備好運行該應用程式。

1. 在工作資料夾的命令提示字元中，執行下列命令：

    ```cmd/sh
    python FileUpload.py
    ```

2. 下列螢幕擷取畫面顯示 **FileUpload** 應用程式的輸出：

    ![simulated-device 應用程式的輸出](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. 您可以使用入口網站檢視您所設定之儲存體容器中的上傳檔案：

    ![上傳的檔案](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的檔案上傳功能來簡化從裝置上傳檔案。 您可以利用下列文章繼續探索 IoT 中樞功能和案例：

* [以程式設計方式建立 IoT 中樞](iot-hub-rm-template-powershell.md)

* [C SDK 簡介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

透過以下連結瞭解有關 Azure Blob 儲存的詳細資訊:

* [Azure Blob 儲存文件](https://docs.microsoft.com/azure/storage/blobs/)

* [Python API 文件的 Azure Blob 儲存](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
