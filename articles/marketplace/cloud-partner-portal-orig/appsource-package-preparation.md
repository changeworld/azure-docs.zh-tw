---
title: AppSource 套件準備 |Azure Marketplace
description: 說明如何準備及建置 AppSource 套件。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280604"
---
# <a name="appsource-package-preparation"></a>AppSource 套件準備

除了 solution.zip 檔案之外，您還需要 **AppSource 套件**。 這是一個 .zip 檔案，其中包含自動化將解決方案部署到客戶的 CRM 環境之程序所需的所有資產。 此 **AppSource 套件**包含下列元件

* 適用於 Package Deployer 的套件
* **Content_Types.xml** 檔案，包含您所使用的資產檔案
* xml 檔案，包含您的應用程式專屬資料
* 32x32 標誌將在系統管理中心中與您的清單一起顯示
* 授權條款、隱私權原則

下列步驟將協助您建立 AppSource 套件。

## <a name="a-create-a-package-for-the-package-deployer"></a>a. 建立 Package Deployer 的套件

Package Deployer 的套件是 AppSource 套件的一部分。

若要建立套件部署器的封裝，請使用下列指示： [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)。 完成時，您的套件將包含下列資產：

1. 套件資料夾：包含所有的解決方案、組態資料、一般檔案，以及您套件的內容。 _注意：在接下來的範例中，我們假設套件資料夾名為 "PkgFolder"_
2. dll：包含套件自訂程式碼的組件。 _注意：在接下來的範例中，我們假設此檔案名為 "MicrosoftSample.dll"。_

現在，您需要建立一個名為 "**Content_Types.xml**" 的檔案。此檔案將列出所有屬於您套件的資產擴充功能。 以下是檔案的範例程式碼。

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

最後一個步驟是將下列內容壓縮成一個檔案。 稱為 **package.zip**。 它會包含

1. PkgFolder (包括資料夾內的所有項目)
2. dll
3. **Content_Types.xml**

建立 package.zip 的步驟：

1. 將您的套件資料夾、**Content_Types.xml** 檔案和 PackageName.dll 放入一個目錄中。

![CRMScreenShot2](media/CRMScreenShot2.png)

1. 選取資料夾中的所有項目，以滑鼠右鍵按一下，然後選擇 [傳送到壓縮 (zip) 資料夾]

![CRMScreenShot3](media/CRMScreenShot3.png)

1. 將名稱變更為 package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. 建立 AppSource 套件

AppSource 套件需要一些額外的檔案。

1. jpg (32x32 解析度)
2. html (HTML 格式的檔案)
3. **Content_Types.xml** (同上)
4. Xml

以下是 input.xml 的範例程式碼。 請參閱下表中的定義。

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**其中：**

|欄位|詳細資料|
|---|---|
|ProviderName|解決方案的來源是誰？ 如果是 Microsoft 小組，那應該是 Microsoft。|
|PackageFile |與 content\_types.xml 檔案一起壓縮的 Package Deployer 資產。 此 zip 檔案應包含 Package Deployer 組件，以及包含 Package Deployer 資產的資料夾。 也就是 package.zip|
|SolutionAnchorName |Package Deployer 中用於解決方案資產顯示名稱和描述的解決方案 zip 檔案名稱。|
| StartDate| 這是方案套件提供使用的日期。 格式為 MM/DD/YYYY|
|EndDate|這是方案套件將停止使用的日期。 格式為 MM/DD/YYYY |
|SupportedCountries |這是以逗號分隔的國家/地區清單，其中應會顯示此套件。 請連絡線上服務，以取得一份所有目前的國家/地區代碼的清單。 在撰寫本文時，清單是：AE、AL、AM、AO、AR、AT、AU、AZ、BA、BB、BD、BE、BG、BH、BM、BN、BO、BR、BY、CA、CH、CI、CL、CM、CO、CR、CV、CW、CY、CZ、DE、DK、DO、DZ、EC、EE、EG、ES、FI、FR、GB、GE、GH、GR、GT、HK、HN、HR、HU、ID、IE、IL、IN、IQ、IS、IT、JM、JO、JP、KE、KG、KN、KR、KW、KY、KZ、LB、LK、LT、LU、LV、LY、MA、MC、MD、ME、MK、MN、MO、MT、MU、MX、MY、NG、NI、NL、NO、NZ、OM、PA、PE、PH、PK、PL、PR、PS、PT、PY、QA、RO、RS、RU、RW、SA、SE、SG、SI、SK、SN、SV、TH、TM、TN、TR、TT、TW、UA、US、UY、UZ、VE、VI、VN、ZA、ZW |
|LearnMoreLink | 此套件的詳細資訊頁面 URL。 |
|Locales|針對您要在慣用方案 UX 中支援之每種 UX 語言的此節點的執行個體。 此節點包含描述每種語言之地區設定、標誌和字詞的子系|
|地區設定：PackageLocale.Code|此節點之語言的 LCID。 範例：美式英文為 1033|
|地區設定：PackageLocale.IsDefault|指出這是預設語言。 如果客戶選擇的 UX 語言無法使用，則將其用作回復語言。|
|地區設定：標誌|這是您想要針對此套件使用的標誌。 圖示的大小為 32x32。 允許的格式有 PNG 和 JPG|
|地區設定：字詞：PackageTerm.File|這是包含您的授權條款的 HTML 文件的檔案名稱。|

這是標誌顯示的位置：

![CRMScreenShot5](media/CRMScreenShot5.png)

最後一個步驟是將下列內容壓縮成一個檔案。

1. zip (稍早建立)
2. **Content_Types.xml**
3. Xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

將檔案重新命名為適合您的應用程式的名稱。 我們希望您包含公司名稱和應用程式名稱。 例如：**_Microsoft_SamplePackage.zip**。
