---
title: 核心 IO 操作 |微軟 Azure 地圖
description: 瞭解如何使用空間 IO 模組中的核心庫高效地讀取和寫入 XML 和分隔資料。
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371449"
---
# <a name="core-io-operations"></a>核心 IO 操作

除了提供讀取空間資料檔的工具外，空間 IO 模組還公開核心基礎庫以快速高效地讀取和寫入 XML 和分隔資料。

命名`atlas.io.core`空間包含兩個低級類，可以快速讀取和寫入 CSV 和 XML 資料。 這些基類為空間 IO 模組中的空間資料讀取器和編寫器提供動力。 隨意使用它們為 CSV 或 XML 檔添加額外的讀取和寫入支援。
 
## <a name="read-delimited-files"></a>讀取已分隔的檔

類`atlas.io.core.CsvReader`讀取包含分隔資料集的字串。 此類提供兩種讀取資料的方法：

- 該`read`函數將讀取完整資料集，並返回表示分隔資料集的所有儲存格的二維字串陣列。
- 函數`getNextRow`讀取分隔資料集中的每行文本，並返回表示該資料集中所有儲存格的字串陣列。 在處理下一行之前，使用者可以處理該行並釋放該行中不需要的任何記憶體。 因此，函數更具有記憶體效率。

預設情況下，讀取器將使用逗號字元作為分隔符號。 但是，分隔符號可以更改為任何單個字元或設置為`'auto'`。 `'auto'`設置為 時，讀取器將分析字串中的第一行文本。 然後，它將從下表中選擇最常用的字元作為分隔符號使用。

| | |
| :-- | :-- |
| Comma (逗號) | `,` |
| 索引標籤 | `\t` |
| Pipe | `|` |

此讀取器還支援用於處理包含分隔符號字元的儲存格的文本限定詞。 引號`'"'`（ ） 字元是預設文本限定詞，但可以更改為任何單個字元。

## <a name="write-delimited-files"></a>寫入分隔檔

將`atlas.io.core.CsvWriter`物件陣列寫入分隔字串。 任何單個字元都可以用作分隔符號或文本限定詞。 預設分隔符號為逗號 （`','`）， 預設文本限定詞是引號`'"'`（ ） 字元。

要使用此類，請按照以下步驟操作：

- 創建類的實例，並可以選擇設置自訂分隔符號或文本限定詞。
- 使用`write`函數或`writeRow`函數將資料寫入類。 對於函數`write`，傳遞表示多個行和儲存格的物件的二維陣列。 要使用`writeRow`函數，傳遞表示具有多列的資料行的物件陣列。
- 調用函數`toString`以檢索分隔字串。 
- 任由選擇，`clear`調用方法以使編寫器可重用並減少其資源配置，或調用`delete`方法以釋放編寫器實例。

> [!Note]
> 寫入的列數將限制為傳遞給寫入器的資料的第一行中的儲存格數。

## <a name="read-xml-files"></a>讀取 XML 檔

與`atlas.io.core.SimpleXmlReader`分析 XML 檔相比`DOMParser`，該類在解析 XML 檔時速度更快。 但是，`atlas.io.core.SimpleXmlReader`該類要求 XML 檔案格式設置好。 格式不好的 XML 檔（例如缺少關閉標記）可能會導致錯誤。

以下代碼演示如何使用 類`SimpleXmlReader`將 XML 字串解析為 JSON 物件並將其序列化為所需的格式。

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>寫入 XML 檔

該`atlas.io.core.SimpleXmlWriter`類以高效記憶體的方式編寫格式良好的 XML。

以下代碼演示如何使用 類`SimpleXmlWriter`生成格式良好的 XML 字串。

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

從上述代碼生成的 XML 如下所示。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [簡單Xml閱讀器](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [簡單XmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [支援的資料格式詳細資訊](spatial-io-supported-data-format-details.md)