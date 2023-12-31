Объекты String являются неизменяемыми, поэтому все операции, которые изменяют строки, фактически приводят к созданию новой строки, что сказывается на производительности приложения. Для решения этой проблемы, чтобы работа со строками проходила с меньшими издержками в Java были добавлены классы StringBuffer и StringBuilder. По сути они напоминает расширяемую строку, которую можно изменять без ущерба для производительности.

Эти классы похожи, практически двойники, они имеют одинаковые конструкторы, одни и те же методы, которые одинаково используются. Единственное их различие состоит в том, что класс `StringBuffer` синхронизированный и потокобезопасный. То есть класс StringBuffer удобнее использовать в многопоточных приложениях, где объект данного класса может меняться в различных потоках. Если же речь о многопоточных приложениях не идет, то лучше использовать класс StringBuilder, который не потокобезопасный, но при этом работает быстрее, чем StringBuffer в однопоточных приложениях.

StringBuffer определяет четыре конструктора:

```Java
StringBuffer()
StringBuffer(int capacity)
StringBuffer(String str)
StringBuffer(CharSequence chars)
```

Аналогичные конструкторы определяет StringBuilder:

```Java
StringBuilder()
StringBuilder(int capacity)
StringBuilder(String str)
StringBuilder(CharSequence chars)
```

Рассмотрим работу этих классов на примере функциональности StringBuffer.

При всех операциях со строками StringBuffer / StringBuilder перераспределяет выделенную память. И чтобы избежать слишком частого перераспределения памяти, StringBuffer/StringBuilder заранее резервирует некоторую область памяти, которая может использоваться. Конструктор без параметров резервирует в памяти место для 16 символов. Если мы хотим, чтобы количество символов было иным, то мы можем применить второй конструктор, который в качестве параметра принимает количество символов.

Третий и четвертый конструкторы обоих классов принимают строку и набор символов, при этом резервируя память для дополнительных 16 символов.

С помощью метода capacity() мы можем получить количество символов, для которых зарезервирована память. А с помощью метода ensureCapacity() изменить минимальную емкость буфера символов:

```Java
String str = "Java";
StringBuffer strBuffer = new StringBuffer(str);
System.out.println("Емкость: " + strBuffer.capacity()); // 20
strBuffer.ensureCapacity(32);
System.out.println("Емкость: " + strBuffer.capacity()); // 42
System.out.println("Длина: " + strBuffer.length()); // 4
```

Так как в самом начале StringBuffer инициализируется строкой "Java", то его емкость составляет 4 + 16 = 20 символов. Затем мы увеличиваем емкость буфера с помощью вызова `strBuffer.ensureCapacity(32)` повышаем минимальную емкость буфера до 32 символов. Однако финальная емкость может отличаться в большую сторону. Так, в данном случае я получаю емкость не 32 и не 32 + 4 = 36, а 42 символа. Дело в том, что в целях повышения эффективности Java может дополнительно выделять память.

Но в любом случае вне зависимости от емкости длина строки, которую можно получить с помощью метода `length()`, в StringBuffer остается прежней - 4 символа (так как в "Java" 4 символа).

Чтобы получить строку, которая хранится в StringBuffer, мы можем использовать стандартный метод `toString()`:

```Java
String str = "Java";
StringBuffer strBuffer = new StringBuffer(str);
System.out.println(strBuffer.toString()); // Java
```

По всем своим операциям StringBuffer и StringBuilder напоминают класс String.

### Получение и установка символов

Метод charAt() получает, а метод setCharAt() устанавливает символ по определенному индексу:

```Java
StringBuffer strBuffer = new StringBuffer("Java");
char c = strBuffer.charAt(0); // J
System.out.println(c);
strBuffer.setCharAt(0, 'c');
System.out.println(strBuffer.toString()); // cava
```

Метод getChars() получает набор символов между определенными индексами:

```Java
StringBuffer strBuffer = new StringBuffer("world");
int startIndex = 1;
int endIndex = 4;
char[] buffer = new char[endIndex-startIndex];
strBuffer.getChars(startIndex, endIndex, buffer, 0);
System.out.println(buffer); // orl
```

### Добавление в строку

Метод append() добавляет подстроку в конец StringBuffer:

```Java
StringBuffer strBuffer = new StringBuffer("hello");
strBuffer.append(" world");
System.out.println(strBuffer.toString()); // hello world
```

Метод insert() добавляет строку или символ по определенному индексу в StringBuffer:

```Java
StringBuffer strBuffer = new StringBuffer("word");

strBuffer.insert(3, 'l');
System.out.println(strBuffer.toString()); //world

strBuffer.insert(0, "s");
System.out.println(strBuffer.toString()); //sworld
```

### Удаление символов

Метод delete() удаляет все символы с определенного индекса о определенной позиции, а метод deleteCharAt() удаляет один символ по определенному индексу:

```Java
StringBuffer strBuffer = new StringBuffer("assembler");
strBuffer.delete(0,2);
System.out.println(strBuffer.toString()); //sembler

strBuffer.deleteCharAt(6);
System.out.println(strBuffer.toString()); //semble
```

### Обрезка строки

Метод substring() обрезает строку с определенного индекса до конца, либо до определенного индекса:

```Java
StringBuffer strBuffer = new StringBuffer("hello java!");
String str1 = strBuffer.substring(6); // обрезка строки с 6 символа до конца
System.out.println(str1); //java!

String str2 = strBuffer.substring(3, 9); // обрезка строки с 3 по 9 символ
System.out.println(str2); //lo jav
```

### Изменение длины

Для изменения длины StringBuffer (не емкости буфера символов) применяется метод setLength(). Если StringBuffer увеличивается, то его строка просто дополняется в конце пустыми символами, если уменьшается - то строка по сути обрезается:

```Java
StringBuffer strBuffer = new StringBuffer("hello");

strBuffer.setLength(10);
System.out.println(strBuffer.toString()); //"hello     "

strBuffer.setLength(4);
System.out.println(strBuffer.toString()); //"hell"
```

### Замена в строке

Для замены подстроки между определенными позициями в StringBuffer на другую подстроку применяется метод replace():

```Java
StringBuffer strBuffer = new StringBuffer("hello world!");
strBuffer.replace(6,11,"java");
System.out.println(strBuffer.toString()); //hello java!
```

Первый параметр метода replace указывает, с какой позиции надо начать замену, второй параметр - до какой позиции, а третий параметр указывает на подстроку замены.

### Обратный порядок в строке

Метод reverse() меняет порядок в StringBuffer на обратный:

```Java
StringBuffer strBuffer = new StringBuffer("assembler");
strBuffer.reverse();
System.out.println(strBuffer.toString()); //relbmessa
```

