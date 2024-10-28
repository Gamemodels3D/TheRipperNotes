# Формат текстур TEXTURE

Файлы с расширением `texture` содержат, как бы это не выглядело странно, текстуры. Это унифицированные контейнеры для хранения текстур различных форматов.

## Заголовок файла  


Файл начинается с сигнатуры `FT2E`

```
HEADER {
  char[4] signature   // "FT2E"
  uint32  version     // версия контейнера, текущая 0x00000007   
}
```

## Информация о текстуре

```
TEXTURE {
  uint32 width           // ширина изображения
  uint32 height          // высота избражения 
  uint8 skip             // неизвестно
  uint8 mipCount         // количество мипмапов 
  ALPHA_MODE alphaMode   // режим прозрачности 
  TEX_TYPE texType       // тип текстуры 
  uint8 texCount         // количество текстур (0x01 обычная, 0x06 кубическая) 
  WRAP_MODE wrapS        // режим обёртывания по U (горизонтали)
  WRAP_MODE wrapT        // режим обёртывания по V (вертикали)
  WRAP_MODE wrapR        // режим обёртывания по W 
  char[4] format         // формат текстуры
  byte[4] colorHint      // ???
  uint32[mipCount] imageSize   // размеры мипмапов
  byte[mipCount][] imageData   // данные мипмапов
}
```

### Режим прозрачности

```
uint8 ALPHA_MODE (
  0x01 = OPAQUE
  0x02 = MASKED
  0x03 = TRANSPARENT
)
```

### Тип текстуры

```
uint8 TEX_TYPE (
  0x00 = ONESIDED
  0x01 = TWOSIDED
  0x02 = CUBEMAP
  0x03 = VOLUME
)
```

### Режим обёртывания

```
uint8 WRAP_MODE (
  0x01 = CLAMP_TO_EDGE
  0x03 = REPEAT
)
```

### Формат текстуры

```
char[4] TEX_FORMAT (
  "1txd" = DXT1
  "3txd" = DXT3
  "5txd" = DXT5
  "abgr" = A8R8G8B8
  "f4dh" = HD4F(?)
)
```

## Источники

1. <span class="mr-2 flex-self-stretch d-none d-md-block no-wrap overflow-x-hidden">TrainzTexture</span> [https://github.com/rileyzzz/TrainzTexture](https://github.com/rileyzzz/TrainzTexture)