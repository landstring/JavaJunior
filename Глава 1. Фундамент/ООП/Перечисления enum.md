
Кроме отдельных примитивных типов данных и классов в Java есть такой тип как enum или перечисление. Перечисления представляют набор логически связанных констант. Объявление перечисления происходит с помощью оператора enum, после которого идет название перечисления. Затем идет список элементов перечисления через запятую:

```Java
enum Day{

    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY
}
```

Перечисление фактически представляет новый тип, поэтому мы можем определить переменную данного типа и использовать ее:

```Java
public class Program{
    public static void main(String[] args) {
        Day current = Day.MONDAY;
        System.out.println(current);    // MONDAY
    }
}

enum Day{

    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY
}
```

Перечисления могут использоваться в классах для хранения данных:

```Java
public class Program{
    public static void main(String[] args) {
        Book b1 = new Book("War and Peace", "L. Tolstoy", Type.BELLETRE);
        System.out.printf("Book '%s' has a type %s", b1.name, b1.bookType);

        switch(b1.bookType){
            case BELLETRE:
                System.out.println("Belletre");
                break;

            case SCIENCE:
                System.out.println("Science");
                break;

            case SCIENCE_FICTION:
                System.out.println("Science fiction");
                break;

            case PHANTASY:
                System.out.println("Phantasy");
                break;
                
        }
    }
}

class Book{

    String name;
    Type bookType;
    String author;

    Book(String name, String author, Type type){
        bookType = type;
        this.name = name;
        this.author = author;
    }
}

enum Type
{

    SCIENCE,
    BELLETRE,
    PHANTASY,
    SCIENCE_FICTION
}
```

Само перечисление объявлено вне класса, оно содержит четыре жанра книг. Класс Book кроме обычных переменных содержит также переменную типа нашего перечисления. В конструкторе мы ее также можем присвоить, как и обычные поля класса.

С помощью конструкции switch..case можно проверить принадлежность значения bookType определенной константе перечисления.

### Методы перечислений

Каждое перечисление имеет статический метод values(). Он возвращает массив всех констант перечисления:

```Java
public class Program{
    public static void main(String[] args) {
        Type[] types = Type.values();
        for (Type s : types) { System.out.println(s); }
    }
}

enum Type
{

    SCIENCE,
    BELLETRE,
    PHANTASY,
    SCIENCE_FICTION
}
```

Метод ordinal() возвращает порядковый номер определенной константы (нумерация начинается с 0):

```Java
System.out.println(Type.BELLETRE.ordinal());    // 1
```

### Конструкторы, поля и методы перечисления

Перечисления, как и обычные классы, могут определять конструкторы, поля и методы. Например:

```Java
public class Program{
    public static void main(String[] args) {
        System.out.println(Color.RED.getCode());        // #FF0000
        System.out.println(Color.GREEN.getCode());      // #00FF00
    }
}

enum Color{
    RED("#FF0000"), BLUE("#0000FF"), GREEN("#00FF00");
    
    private String code;
    
    Color(String code){
        this.code = code;
    }

    public String getCode(){ return code;}
}
```

Перечисление Color определяет приватное поле code для хранения кода цвета, а с помощью метода getCode оно возвращается. Через конструктор передается для него значение. Следует отметить, что конструктор по умолчанию приватный, то есть имеет модификатор private. Любой другой модификатор будет считаться ошибкой. Поэтому создать константы перечисления с помощью конструктора мы можем только внутри перечисления.

Также можно определять методы для отдельных констант:

```Java
public class Program{
    public static void main(String[] args) {
        Operation op = Operation.SUM;
        System.out.println(op.action(10, 4));   // 14
        op = Operation.MULTIPLY;
        System.out.println(op.action(6, 4));    // 24
    }
}

enum Operation{
    SUM{
        public int action(int x, int y){ return x + y;}
    },

    SUBTRACT{
        public int action(int x, int y){ return x - y;}
    },

    MULTIPLY{
        public int action(int x, int y){ return x * y;}
    };

    public abstract int action(int x, int y);
}
```

