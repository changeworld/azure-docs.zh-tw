---
title: 使用服務管理 API (Python) - 功能指南
description: 了解如何透過程式設計從 Python 執行一般服務管理工作。
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 50501413a63921a9a34be1c04ed259990922b686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70141475"
---
# <a name="use-service-management-from-python"></a>從 Python 使用服務管理
本指南說明如何以程式設計方式，從 Python 執行一般服務管理工作。 [適用於 Python 的 Azure SDK](https://github.com/Azure/azure-sdk-for-python) \(英文\) 中的 **ServiceManagementService** 類別支援以程式設計方式存取 [Azure 入口網站][management-portal]中提供的大部分服務管理相關功能。 您可以使用此功能來建立、更新和刪除雲端服務、部署、資料管理服務，以及虛擬機器。 建置需要透過程式設計方式存取服務管理的應用程式時，此功能十分實用。

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>什麼是服務管理？
「Azure 服務管理 API」可讓使用者以程式設計方式存取透過 [Azure 入口網站][management-portal]提供的大部分服務管理功能。 您可以使用「適用於 Python 的 Azure SDK」來管理雲端服務和儲存體帳戶。

若要使用服務管理 API，您必須 [建立 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="concepts"></a><a name="Concepts"> </a>概念
「適用於 Python 的 Azure SDK」含有[服務管理 API][svc-mgmt-rest-api]，這是一個 REST API。 所有 API 作業都會透過 SSL 執行，並使用 X.509 v3 憑證來相互驗證。 您可以從在 Azure 中執行的服務內存取此管理服務。 此外，也可以透過網際網路，從任何可傳送 HTTPS 要求和接收 HTTPS 回應的應用程式，直接存取此管理服務。

## <a name="installation"></a><a name="Installation"> </a>安裝
本文中所述的所有功能都可在 `azure-servicemanagement-legacy` 套件中找到，您可以使用 pip 來安裝此套件。 如需有關安裝的詳細資訊 (例如，若您不熟悉 Python)，請參閱[安裝 Python 和 Azure SDK](/azure/python/python-sdk-azure-install)。

## <a name="connect-to-service-management"></a><a name="Connect"> </a>連線到服務管理
若要連線到服務管理端點，您必須具備 Azure 訂用帳戶 ID 和有效的管理憑證。 您可以透過 [Azure 入口網站][management-portal]取得訂用帳戶識別碼。

> [!NOTE]
> 目前在 Windows 上執行時，您可以使用以 OpenSSL 建立的憑證。 需要 Python 2.7.4 或更新版本。 建議您使用 OpenSSL 而非 .pfx，因為未來可能會移除對 .pfx 憑證的支援。
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Windows/Mac/Linux 上的管理憑證 (OpenSSL)
您可以使用 [OpenSSL](https://www.openssl.org/) 建立管理憑證。 您需要建立兩個憑證，一個用於伺服器 (`.cer` 檔案)，一個用於用戶端 (`.pem` 檔案)。 若要建立 `.pem` 檔案，請執行下列命令：

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

若要建立 `.cer` 憑證，請執行下列命令：

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

如需有關 Azure 憑證的詳細資訊，請參閱 [Azure 雲端服務的憑證概觀](cloud-services-certs-create.md)。 有關 OpenSSL 參數的完整說明，請參閱 上的文檔[https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)。

建立這些檔案之後，請將 `.cer` 檔案上傳到 Azure。 在 [Azure 入口網站][management-portal]中的 [設定]**** 索引標籤上，選取 [上傳]****。 請記下您儲存 `.pem` 檔案的位置。

在您取得訂用帳戶 ID 之後，請建立憑證，然後將 `.cer` 檔案上傳到 Azure，連線到 Azure 管理端點。 請將訂用帳戶 ID 和 `.pem` 檔案的路徑傳遞給 **ServiceManagementService** 來進行連線。

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

在上一個範例中， `sms` 是 **ServiceManagementService** 物件。 **ServiceManagementService** 類別是用來管理 Azure 服務的主要類別。

### <a name="management-certificates-on-windows-makecert"></a>Windows 上的管理憑證 (MakeCert)
您可以使用 `makecert.exe`，在您的電腦上建立自我簽署管理憑證。 請以**系統管理員**身分開啟 **Visual Studio 命令提示字元**，然後使用下列命令 (將 *AzureCertificate* 取代為您想要使用的憑證名稱)：

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

此命令會建立 `.cer` 檔案，並將其安裝在 [個人]**** 憑證存放區中。 如需詳細資訊，請參閱 [Azure 雲端服務的憑證概觀](cloud-services-certs-create.md)。

建立憑證之後，請將 `.cer` 檔案上傳到 Azure。 在 [Azure 入口網站][management-portal]中的 [設定]**** 索引標籤上，選取 [上傳]****。

在您取得訂用帳戶 ID 之後，請建立憑證，然後將 `.cer` 檔案上傳到 Azure，連線到 Azure 管理端點。 將訂用帳戶 ID 和您 [個人]**** 憑證存放區中憑證的位置傳遞給 **ServiceManagementService** (同樣地，將 *AzureCertificate* 取代為您憑證的名稱) 來進行連線。

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

在上一個範例中， `sms` 是 **ServiceManagementService** 物件。 **ServiceManagementService** 類別是用來管理 Azure 服務的主要類別。

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>列出可用的位置
若要列出可用來裝載服務的位置，請使用 **list\_locations** 方法。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

建立雲端服務或儲存體服務時，您必須提供有效的位置。 **list\_locations** 方法一律會傳回目前可用位置的最新清單。 截至本文撰寫時間為止，可用位置如下：

* 西歐
* 北歐
* 東南亞
* 東亞
* 美國中部
* 美國中北部
* 美國中南部
* 美國西部
* 美國東部
* 日本東部
* 日本西部
* 巴西南部
* 澳大利亞東部
* 澳大利亞東南部

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>建立雲端服務
當您建立應用程式並在 Azure 中執行它時，其程式碼和設定合稱為 Azure [雲端服務][cloud service]。 （在早期 Azure 版本中稱為*託管服務*。可以使用**\_創建託管\_服務**方法創建新託管服務。 請提供託管服務名稱 (在 Azure 中必須是唯一的)、標籤 (自動編碼為 base64)、描述及位置來建立此服務。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

您可以使用**\_清單託管\_服務**方法列出訂閱的所有託管服務。

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

若要取得特定託管服務的相關資訊，請將託管服務名稱傳遞給 **get\_hosted\_service\_properties** 方法。

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

在建立雲端服務之後，請使用 **create\_deployment** 方法將程式碼部署至服務。

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>刪除雲端服務
通過將服務名稱傳遞給**\_刪除託管\_服務**方法，可以刪除雲服務。

    sms.delete_hosted_service('myhostedservice')

您必須先刪除服務的所有部署，才能刪除該服務。 如需詳細資訊，請參閱[刪除部署](#DeleteDeployment)。

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>刪除部署
若要刪除部署，請使用 **delete\_deployment** 方法。 下列範例示範如何刪除名為 `v1` 的部署：

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>建立儲存體服務
[存儲服務](../storage/common/storage-create-storage-account.md)允許您訪問 Azure [blob、](../storage/blobs/storage-python-how-to-use-blob-storage.md)[表](../cosmos-db/table-storage-how-to-use-python.md)和[佇列](../storage/queues/storage-python-how-to-use-queue-storage.md)。 若要建立儲存服務，您必須要有服務的名稱 (3 到 24 個小寫字元，且在 Azure 中是唯一的)。 此外，您還需要描述、標籤 (最多 100 個字元，會自動編碼為 base64) 及位置。 下列範例說明如何藉由指定位置來建立儲存服務：

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

在上一個範例中，可將 **create\_storage\_account** 所傳回的結果傳遞給 **get\_operation\_status** 方法，以擷取 **create\_storage\_account** 作業的狀態。 

您可以使用**\_清單存儲\_帳戶**方法列出存儲帳戶及其屬性。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>刪除儲存體服務
若要刪除儲存體服務，請將儲存服務名稱傳遞給 **delete\_storage\_account** 方法。 如果刪除儲存體服務，則會刪除該服務中儲存的所有資料 (Blob、資料表和佇列)。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>列出可用的作業系統
要列出可用於託管服務的作業系統，請使用**清單\_作業系統\_** 方法。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

或者，您可以使用**\_清單作業系統\_\_族**方法，該方法按族對作業系統進行分組。

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>建立作業系統映像
要將作業系統映射添加到映射存儲庫，請使用**add\_os\_映射**方法。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

若要列出可用的作業系統映像，請使用 **list\_os\_images** 方法。 它會包括所有平台映像和使用者映像。

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>刪除作業系統映像
要刪除使用者圖像，請使用**刪除\_os\_圖像**方法。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>創建虛擬機器
若要建立虛擬機器，您必須先建立 [雲端服務](#CreateCloudService)。 接著，請使用 **create\_virtual\_machine\_deployment** 方法來建立虛擬機器部署。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>刪除虛擬機器
若要刪除虛擬機器，您必須先使用 **delete\_deployment** 方法來刪除部署。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

接著，可以使用 **delete\_hosted\_service** 方法來刪除雲端服務。

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>從擷取的虛擬機器映像建立虛擬機器
要捕獲 VM 映射，請首先調用**捕獲\_\_vm 映射**方法。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

若要確定您已成功擷取映像，請使用 **list\_vm\_images**API。 請確定您的映像顯示在結果中。

    images = sms.list_vm_images()

最後，若要使用所擷取的映像來建立虛擬機器，請如先前一樣，使用 **create\_virtual\_machine\_deployment** 方法，但這次改為傳入 vm_image_name。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

若要深入了解如何在傳統部署模型中擷取 Linux 虛擬機器，請參閱[擷取 Linux 虛擬機器](../virtual-machines/linux/classic/capture-image-classic.md)。

若要深入了解如何在傳統部署模型中擷取 Windows 虛擬機器，請參閱[擷取 Windows 虛擬機器](../virtual-machines/windows/classic/capture-image-classic.md)。

## <a name="next-steps"></a><a name="What's Next"> </a>後續步驟
現在，您已經瞭解了服務管理的基礎知識，您可以訪問[Azure Python SDK 的完整 API 參考文檔](https://azure-sdk-for-python.readthedocs.org/)，並輕鬆執行複雜的任務來管理 Python 應用程式。

如需詳細資訊，請參閱 [Python 開發人員中心](https://azure.microsoft.com/develop/python/)。

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
