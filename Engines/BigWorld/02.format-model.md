# Файлы model

Содержат описания модели в формате xml.

Так выглядит типичный файл `vehicles/british/GB01_Medium_Mark_I/normal/lod0/Hull.model`:

```xml
<xml>
	<materialNames/>
	<visibilityBox>
		<min>-1.43914 -0.648141 -2.69802</min>
		<max>1.43504 1.06742 2.46864</max>
	</visibilityBox>
	<tank>true</tank>
	<nodefullVisual>vehicles/british/GB01_Medium_Mark_I/normal/lod0/Hull</nodefullVisual>
	<parent>vehicles/british/GB01_Medium_Mark_I/normal/lod1/Hull</parent>
	<extent>32</extent>
	<projAreaCoeff>1</projAreaCoeff>
</xml>
```

## Секции

### &lt;materialNames&gt;

Обычно пуста.

### &lt;visibilityBox&gt;

Координаты ограничивающего паралеллепипеда.

### &lt;tank&gt;

Логическое значение установленное в TRUE если модель относится к игровой технике(?)

### &lt;nodefullVisual&gt;

Имя `visual` файла с узлами.

### &lt;nodelessVisual&gt;

Имя `visual` файла без узлов. В файле есть либо &lt;nodefullVisual&gt;, либо &lt;nodelessVisual&gt;, но не оба сразу.

### &lt;parent&gt;

Имя `model` файла следующего более низкого уровня детализации.

### &lt;extent&gt;

Уровень детализации(?). Степень двойки(?).

### &lt;projAreaCoeff&gt;   


Встречается редко. Содержание неизвестно.

## Данные

Представляют интерес только `<nodefullVisual>` и `<nodelessVisual>`. Однако, в общем случае достаточно просто поменять расширение файла на `.visual`, чтобы получить нужный visual файл.