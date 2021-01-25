---
title: Azure API for FHIR 的資料轉換
description: 使用 $convert 資料端點和自訂轉換子範本，以轉換 Azure API for FHIR 中的資料。
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: c794af26fdfe2d3706d8d8d266d0756eff391b50
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747676"
---
# <a name="how-to-convert-data-to-fhir"></a>如何將資料轉換為 FHIR

Azure API for FHIR 中的 $convert 資料自訂端點適用于從不同格式轉換成 FHIR 的資料。 它會使用來自 [FHIR 轉換器](https://github.com/microsoft/FHIR-Converter) 專案的液體範本引擎和範本作為預設範本。 您可以視需要自訂這些轉換範本。 目前支援 HL7v2 對 FHIR 的轉換。

## <a name="use-the-convert-data-endpoint"></a>使用 $convert 資料端點

`https://<<FHIR service base URL>>/$convert-data`

$convert 資料會採用要求主體中的 [參數](http://hl7.org/fhir/parameters.html) 資源，如下所述：

**參數資源：**

| 參數名稱      | 描述 | 接受的值 |
| ----------- | ----------- | ----------- |
| inputData      | 要轉換的資料。 | JSON 字串資料類型的有效值|
| inputDataType   | 輸入的資料類型。 | ```HL7v2``` |
| templateCollectionReference | 樣板集合的參考。 您可以參考 **預設範本**，或向 Azure API for FHIR 註冊的自訂範本映射。 請參閱下列資訊，以瞭解如何自訂範本、在 ACR 上裝載這些範本，以及註冊 Azure API for FHIR。  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | 轉換資料時要使用的根範本。 | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> 預設範本可協助您快速開始使用。 不過，當我們升級 Azure API for FHIR 時，這些更新可能會更新。 若要在不同版本的 Azure API for FHIR 之間擁有一致的資料轉換行為，您必須在 Azure Container Registry 上裝載您自己的範本複本、將它們註冊到 Azure API for FHIR，然後在您的 API 呼叫中使用，如稍後所述。

**範例要求：**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**範例回應：**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>自訂範本

您可以使用 Visual Studio Code 的 [FHIR 轉換器延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) 模組，依據您的需求自訂範本。 此延伸模組提供互動式編輯體驗，並可讓您輕鬆地下載 Microsoft 發佈的範本和範例資料。 如需詳細資訊，請參閱擴充功能中的檔。

## <a name="host-and-use-templates"></a>裝載和使用範本

強烈建議您在 ACR 上裝載自己的範本複本。 裝載您自己的範本複本和在 $convert 資料作業中使用這些範本有四個步驟：

1. 將範本推送到您的 Azure Container Registry。
1. 在您的 Azure API for FHIR 實例上啟用受控識別。
1. 將 ACR 的存取權提供給 Azure API for FHIR 受控識別。
1. 在 Azure API for FHIR 中註冊 ACR 伺服器。

### <a name="push-templates-to-azure-container-registry"></a>將範本推送至 Azure Container Registry

建立 ACR 實例之後，您可以使用 [FHIR 轉換器延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)模組中的 [ _FHIR 轉換器： push Templates_ ] 命令，將自訂的範本推送至 ACR。 或者，您也可以使用「 [範本管理 CLI」工具](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) 來進行此用途。

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 上啟用受控識別

在 Azure 入口網站中流覽至 Azure API for FHIR 服務的實例，然後選取 [身分 **識別** ] 分頁。
將狀態變更為 [ **開啟** ]，以在 Azure API for FHIR 中啟用受控識別。

![啟用受控識別](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>提供 ACR 的存取權給 Azure API for FHIR

在您的 ACR 實例中流覽至 [存取控制] (IAM) 分頁，然後選取 [ _新增角色指派_]。

![ACR 角色指派](media/convert-data/fhir-acr-role-assignment.png)

將 AcrPull 角色授與您的 Azure API for FHIR 服務實例。

![加入角色](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中註冊 ACR 伺服器

您可以在 Azure API for FHIR 中註冊最多20個 ACR 伺服器。

視需要從 Azure PowerShell 安裝 healthcareapis CLI：

```powershell
az extension add -n healthcareapis
```

註冊 acr 伺服器以 Azure API for FHIR 遵循下列範例：

#### <a name="register-a-single-acr-server"></a>註冊單一 ACR server

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>註冊多個 ACR 伺服器

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>驗證

在 templateCollectionReference 參數中指定您的範本參考，以呼叫 $convert 資料 API。

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>已知問題和因應措施

- 某些預設範本檔案包含 UTF-8 BOM。 因此，產生的識別碼值會包含 BOM 字元。 這可能會導致 FHIR 伺服器發生問題。 因應措施是使用 VS Code 擴充功能來提取 Microsoft 範本，然後將這些範本從 _ID/程式、_ID/_來源_ 和 _ID/_免疫_. 液體移除 BOM 字元 _之後，將_ 這些範本推送至您自己的 ACR。

