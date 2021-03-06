---
title: 获取有关转换模型的信息
description: 所有模型转换参数的说明
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681514"
---
# <a name="get-information-about-a-converted-model"></a>获取有关转换模型的信息

转换服务生成的 arrAsset 文件仅用于呈现服务使用。 但是，有时您可能希望在不启动呈现会话的情况下访问有关模型的信息。 因此，转换服务在输出容器中的 arrAsset 文件旁边放置一个 JSON 文件。 例如，如果转换了文件`buggy.gltf`，输出容器将包含一个在转换后的资产`buggy.info.json``buggy.arrAsset`旁边称为的文件。 它包含有关源模型、转换模型和转换本身的信息。

## <a name="example-info-file"></a>*示例信息*文件

下面是通过转换名为 *：* `buggy.gltf`

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>信息文件中的信息

### <a name="the-files-section"></a>*文件*部分

本节包含提供的文件名。

* `input`：源文件的名称。
* `output`：当用户指定非默认名称时，输出文件的名称。

### <a name="the-conversionsettings-section"></a>*转换设置*部分

本节包含转换模型时指定的[转换设置](configure-model-conversion.md#settings-file)的副本。

### <a name="the-inputinfo-section"></a>*输入信息*部分

本节记录有关源文件格式的信息。

* `sourceAssetExtension`：源文件的文件扩展名。
* `sourceAssetFormat`：源文件格式的说明。
* `sourceAssetFormatVersion`：源文件格式的版本。
* `sourceAssetGenerator`：生成源文件的工具的名称（如果可用）。

### <a name="the-inputstatistics-section"></a>*输入统计信息*部分

本节提供有关源场景的信息。 本节中的值与创建源模型的工具中的等效值之间通常存在差异。 这种差异是预料之中的，因为模型在导出和转换步骤期间被修改。

* `numMeshes`：网格零件的数量，其中每个零件可以引用单个材质。
* `numFaces`：整个模型中的_三角形_总数。 请注意，网格在转换期间进行三角化。
* `numVertices`：整个模型中的顶点总数。
* `numMaterial`：整个模型中的材料总数。
* `numFacesSmallestMesh`：模型最小网格中的三角形数。
* `numFacesBiggestMesh`：模型最大网格中的三角形数。
* `numNodes`：模型场景图中的节点数。
* `numMeshUsagesInScene`：节点引用等号的次数。 多个节点可以引用同一网格。
* `maxNodeDepth`：场景图中节点的最大深度。

### <a name="the-outputinfo-section"></a>*输出信息*部分

本节记录有关生成的输出的一般信息。

* `conversionToolVersion`：模型转换器的版本。
* `conversionHash`：arrAsset 中数据的哈希，有助于呈现。 可用于了解转换服务在同一文件上重新运行时是否产生了不同的结果。

### <a name="the-outputstatistics-section"></a>*输出统计*部分

本节记录从转换后的资产计算的信息。

* `numMeshPartsCreated`：arrAsset 中的模子数。 它可以与`inputStatistics`部分不同`numMeshes`，因为实例化受转换过程的影响。
* `numMeshPartsInstanced`：在 arrAsset 中重复使用的等件数。
* `recenteringOffset`：启用`recenterToOrigin`[转换设置](configure-model-conversion.md)中的选项后，此值是将转换后的模型移回其原始位置的平移。
* `boundingBox`：模型的边界。

## <a name="next-steps"></a>后续步骤

* [模型转换](model-conversion.md)
* [配置模型转换](configure-model-conversion.md)
