# Формат моделей TRAINZMESH

Файлы TRAINZMESH хранят данные геометрии моделей в Trainz.

<span class="HwtZe" lang="ru"><span class="jCAhz ChMk0b"><span class="ryNqvb">Формат файла Trainzmesh — это простой формат, созданный для использования с TANE и TRS19.</span></span> <span class="jCAhz ChMk0b"><span class="ryNqvb">Он заменил более сложный формата файла IM, который использовался Auran Jet в старых версиях продукта Trainz.</span></span></span>

## Заголовок файла  


Файл начинается с сигнатуры `tzMF`

```
HEADER {
  char[4] signature         // "tzMF"
  uint32be version          // версия?  
  uint32be blokaNum         // количество блоков BLOCKA
  uint32be boneNum          // количество костей в скелете
  uint32be meshNum          // количество мешей
  uint32be attachPointNum   // количество точек крепления
  uint32be blokeNum         // количество блоков BLOCKE
  floatbe[6] boundingBox    // ограничиваюший паралеллепипед
}
```

## Скелет  


Содержит данные о скелете в виде массива костей

```
BONE {
  bstring name          // имя кости, без завершающего нуля
  bstring parentName    // имя предка
  floatbe[3] position   // позиция
  floatbe[4] rotation   // кватернион вращения    
}
```

## Меши

Содержит данные о геометрии в виде списка мешей

```
MESH {
  byte[17] unknown0     
  istring format               // формат
  uint32be vertexNum           // количество вершин
  uint32be indiceNum           // количество индексов
  uint32be unknown             // размер данных одной вершины?
  VERTEX[vertexNum] vertices   // массив вершин
  INDEX[indiceNum] indices     // массив индексов
  MATERIAL material            // применяемый материал
}
```

Странно, но в VERTEX и INDEX порядок байтов little endian, хотя остальные параметры хранятся в big endian.

### Формат вершин

Закодирован в строке format, в виде "\[{параметр}{количество}{тип}\]". К примеру, подстрока "P3fWi4B" означает, что сначала идёт позиция вершины `P` из трёх чисел с плавающей точкой `3f`, а следом четыре индекса костей скелета `Wi` в виде байтов `4B`.

#### Параметры

```
S0_ - начало строки формата
P   - координаты вершины
N   - нормаль
T0  - текстурные координаты
Tg  - тангенты
Wi  - индексы костей
Wg  - вес костей
```

### Материалы

```
MATERIAL {
  isring shader                  // имя шейдера с завершающим нулём
  istring name                   // имя текстуры с завершающим нулём
  byte param1                    // не понятно
  ALPHA_MODE alphaMode           // режим прозрачности
  floatbe[4] diffuseColor        // цвет
  uint32be textureNum            // количество текстур
  TEXTURE[textureNum] textures   // текстуры
  uint32be paramsNum             // количество параметров
  PARAM[paramsNum] params        // параметры
}
```

#### Режим прозрачности  


```
uint32be ALPHA_MODE (
  1 = ALPHA_MODE_OPAQUE        // непрозрачный
  2 = ALPHA_MODE_MASKED        // чёрно-белая маска
  3 = ALPHA_MODE_TRANSPARENT   // карта прозрачности
)
```

#### Текстуры

```
TEXTURE {
  istring type   // тип текстуры с завершающим нулём
  istring file   // файл текстуры с завершающим нулём
}
```

#### Параметры

```
PARAM {
  istring name      // имя параметра
  PARAM_TYPE type   // тип параметра
  uint32be num      // количество?
  byte[] data       // значения параметра в зависимости от типа
}
```

##### Типы параметров

```
uint16 PARAM_TYPE (
  0x0000 = PARAM_TYPE_FLOAT    // float
  0x0006 = PARAM_TYPE_FLOAT2   // float[2]
  0x0007 = PARAM_TYPE_FLOAT4   // float[4]
)
```

## Точки крепления

Содержит координаты точек крепления деталей

```
POINT {
  byte nameLength                 // длина имени кости
  char[nameLength] name           // имя кости, без завершающего нуля
  floatbe[3] position             // позиция
  floatbe[4] rotation             // кватернион вращения    
  byte[7] unknown
}
```

## Источники

1. <span dir="auto">Trainzmesh file </span><span dir="auto">[https://online.ts2009.com/mediaWiki/index.php/Trainzmesh\_file](https://online.ts2009.com/mediaWiki/index.php/Trainzmesh_file)</span>