# Формат моделей IM

Файлы IM (Indexed Mesh) хранят данные геометрии моделей в Trainz.

Формат файла изначально был разработан Auran (теперь N3V Games) как часть игрового движка Auran Jet, который использовался для оригинальных симуляторов Trainz. Новый движок E2, представленный в TANE, также использует данные из этого формата файла.

## Заголовок файла  


Файл начинается с сигнатуры `JIRF`

```
HEADER {
  char[4] signature   // "JIRF"
  uint32 dataSize     // длина данных   
}
```

## Данные модели

Начинается с сигнатуры `IDMX`. Далее до конца файла следуют блоки данных.

Каждый блок данных начинается с заголовка

```
CHUNKHEAD {
  char[4] signature    // сигнатура блока
  uint32 dataSize      // длина данных   
  uint32 version       // версия данных
}
```

### Блок общей информации  


Блок данных с сигнатурой `INFO`.

#### Формат версии 102

```
INFO {
  CHUNKHEAD head       
  float[3] position    // позиция
  float[4] rotation    // кватернион вращения    
  uint32 chunkNum      // количество блоков
  float vertexPower    // влияние вершин
  float objectPower    // влияние объекта
}
```

#### Формат версии 104

```
INFO {
  CHUNKHEAD head
  float[3] position    // позиция
  float[4] rotation    // кватернион вращения    
  uint32 chunkNum      // количество блоков
  float vertexPower    // влияние вершин
  float objectPower    // влияние объекта
  float[6] boundingBox // ограничиваюший паралеллепипед
}
```

### Блок данных  


Блок данных с сигнатурой `CHNK`.

#### Формат версии 100

```
CHNK {
  CHUNKHEAD head
  uint32 index         // индекс блока
  MATERIAL material    // данные о материале
  GEOMETRY geometry    // данные о геометрии
}
```

### Блок материала  


Блок данных с сигнатурой `MATL`.

#### Формат версии 102 и 103  


```
MATL {
  CHUNKHEAD head
  uint32 nameLength              // длина имени материала (кратно 4)
  char[nameLength] name          // имя материала, дополненное до nameLength нулями
  uint32 unknown1
  bool32 doubleSided             // двусторонний материал   
  float opacity                  // прозрачность
  float[3] ambientColor          // цвет окружения
  float[3] diffuseColor          // диффузный цвет
  float[3] specularColor         // рассеянный цвет
  float[3] emissiveColor         // цвет свечения
  float gloss                    // блеск
  uint32 textureNum              // количество используемых текстур
  TEXTURE[textureNum] textures   // текстуры
}
```

#### Формат текстуры

```
TEXTURE {
  MAP_TYPE type            // тип текстуры
  uint32 nameLength        // длина имени файла (кратно 4)
  char[nameLength] name    // имя файла, дополненное до nameLength нулями
  float power              // влияние
}
```

##### Типы текстур

```
uint32 MAP_TYPE (
  0x00000001 = MAP_TYPE_DIFFUSE   // диффузная текстура
  0x00000004 = MAP_TYPE_UNKNOWN   //
  0x00000006 = MAP_TYPE_NORMAL    // карта нормали
)
```


### Блок геометрии  


Блок данных с сигнатурой `GEOM`.

#### Формат версии 102  


```
GEOMETRY {
  CHUNKHEAD head
  uint32 unknown1 
  float area                 // площадь фигуры? зачем?
  uint32 vertexNum           // количество вершин     
  uint32 triangleNum         // треугольники?
  uint32 indiceNum           // количество индексов
  uint32 faceNum             // треугольники?
  uint32 unknown3
  VERTEX {
    float[3] position        // позиция вершины
    float[2] uv              // текстурные координаты
  }[vertexNum] vertices      // массив вершин
  uint16[indiceNum] indices  // массив индексов
  byte[] unknown5            // непонятные данные
}
```

#### Формат версии 200

```
GEOMETRY {
  CHUNKHEAD head
  uint32 unknown1 
  float area                 // площадь фигуры? зачем?
  uint32 vertexNum           // количество вершин     
  uint32 triangleNum         // треугольники?
  uint32 indiceNum           // количество индексов
  uint32 faceNum             // треугольники?
  uint32 unknown3
  uint32 boneLength          // длина имени кости скелета (кратно 4)
  char[boneLength] bone      // имя кости, дополненное до boneLength нулями
  VERTEX {
    float[3] position        // позиция вершины
    float[2] uv              // текстурные координаты
  }[vertexNum] vertices      // массив вершин
  uint16[indiceNum] indices  // массив индексов
  byte[] unknown5            // непонятные данные
}
```

#### Формат версии 201

```
GEOMETRY {
  CHUNKHEAD head
  uint32 unknown1 
  uint32 unknown2 
  float area                 // площадь фигуры? зачем?
  uint32 vertexNum           // количество вершин     
  uint32 triangleNum         // треугольники?
  uint32 indiceNum           // количество индексов
  uint32 faceNum             // треугольники?
  uint32 unknown3
  uint32 boneLength          // длина имени кости скелета (кратно 4)
  char[boneLength] bone      // имя кости, дополненное до boneLength нулями
  VERTEX {
    float[3] position        // позиция вершины
    float[2] uv              // текстурные координаты
  }[vertexNum] vertices      // массив вершин
  uint16[indiceNum] indices  // массив индексов
  byte[] unknown5            // непонятные данные
}
```

### Блок костей влияния

Блок с сигнатурой `INFL`.

#### Формат версии 100

```
INFL {
  CHUNKHEAD head
  uint32 bonesNum      // количество костей
  byte[] bones         // данные костей, пока не разобраны
}
```

### Блок точек крепления  


Блок с сигнатурой `ATCH`.

#### Формат версии 100

```
ATCH {
  CHUNKHEAD head
  uint32 pointNum            // количество точек
  POINT {
    uint32 nameLength        // длина имени файла (кратно 4)
    char[nameLength] name    // имя файла, дополненное до nameLength нулями
    float[4][3] matrix       // матрица точки
  }[pointNum] points
}
```

### Блок скелета

Блок с сигнатурой `SKEL`.

#### Формат версии

```
SKEL {
  CHUNKHEAD head
  BONE bone            // данные о костях
}
```

### Блок кости скелета  


Блок с сигнатурой `BONE`.

#### Формат версии 

```
BONE {
  CHUNKHEAD head
  uint32 nameLength       // длина имени кости (кратно 4)
  char[nameLength] name   // имя кости, дополненное до nameLength нулями
  float[3] position       // позиция
  float[4] rotation       // кватернион вращения    
  uint32 childNum         // количество костей
  BONE[childNum] bones    // данные о потомках
}
```

## Источники

1. IM files [https://online.ts2009.com/mediaWiki/index.php/IM\_files](https://online.ts2009.com/mediaWiki/index.php/IM_files)
2. Изучение формата im файлов. Советы, примеры, достижения [https://forum.trainzup.net/archive/index.php/t-859.html](https://forum.trainzup.net/archive/index.php/t-859.html)