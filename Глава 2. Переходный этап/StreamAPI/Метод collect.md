Большинство операций класса Stream, которые модифицируют набор данных, возвращают этот набор в виде потока. Однако бывают ситуации, когда хотелось бы получить данные не в виде потока, а в виде обычной коллекции, например, ArrayList или HashSet. И для этого у класса Stream определен метод collect. Первая версия метода принимает в качестве параметра функцию преобразования к коллекции:

```Java
<R,A> R collect(Collector<? super T,A,R> collector)
```

Параметр R представляет тип результата метода, параметр Т - тип элемента в потоке, а параметр А - тип промежуточных накапливаемых данных. В итоге параметр collector представляет функцию преобразования потока в коллекцию.

Эта функция представляет объект Collector, который определен в пакете _java.util.stream_. Мы можем написать свою реализацию функции, однако Java уже предоставляет ряд встроенных функций, определенных в классе Collectors:

- toList(): преобразование к типу List
- toSet(): преобразование к типу Set    
- toMap(): преобразование к типу Map

Например, преобразуем набор в потоке в список:

```Java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.stream.Collectors;

public class Program {

    public static void main(String[] args) {
        List<String> phones = new ArrayList<String>();
        Collections.addAll(phones, "iPhone 8", "HTC U12", "Huawei Nexus 6P",
                "Samsung Galaxy S9", "LG G6", "Xiaomi MI6", "ASUS Zenfone 2",
                "Sony Xperia Z5", "Meizu Pro 6", "Lenovo S850");
        List<String> filteredPhones = phones.stream()
                .filter(s->s.length()<10)
                .collect(Collectors.toList());
        for(String s : filteredPhones){
            System.out.println(s);
        }
    }
}
```

Использование метода `toSet()` аналогично.

```Java
Set<String> filteredPhones = phones.stream()
                .filter(s->s.length()<10)
                .collect(Collectors.toSet());
```

Для применения метода `toMap()` надо задать ключ и значение. Например, пусть у нас есть следующая модель:

```Java
class Phone{

    private String name;
    private int price;

    public Phone(String name, int price){
        this.name=name;
        this.price=price;
    }
    
    public String getName() {
        return name;
    }
    
    public int getPrice() {
        return price;
    }
}
```

Теперь применим метод `toMap()`:

```Java
import java.util.Map;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Program {

    public static void main(String[] args) {
        Stream<Phone> phoneStream = Stream.of(new Phone("iPhone 8", 54000),
            new Phone("Nokia 9", 45000),
            new Phone("Samsung Galaxy S9", 40000),
            new Phone("LG G6", 32000));
            
        Map<String, Integer> phones = phoneStream
            .collect(Collectors.toMap(p->p.getName(), t->t.getPrice()));
        phones.forEach((k,v)->System.out.println(k + " " + v));
    }
}

class Phone{

    private String name;
    private int price;

    public Phone(String name, int price){
        this.name=name;
        this.price=price;
    }

    public String getName() { return name; }
    public int getPrice() { return price; }
}
```

Лямбда-выражение `p->p.getName()` получает значение для ключа элемента, а `t->t.getPrice()` - извлекает значение элемента.

Если нам надо создать какой-то определенный тип коллекции, например, HashSet, то мы можем использовать специальные функции, которые определены в классах-коллекций. Например, получим объект HashSet:

```Java
import java.util.HashSet;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Program {
    public static void main(String[] args) {
        Stream<String> phones = Stream.of("iPhone 8", "HTC U12", "Huawei Nexus 6P",
                "Samsung Galaxy S9", "LG G6", "Xiaomi MI6", "ASUS Zenfone 2",
                "Sony Xperia Z5", "Meizu Pro 6", "Lenovo S850");
        HashSet<String> filteredPhones = phones.filter(s->s.length()<12).
                                  collect(Collectors.toCollection(HashSet::new));
        filteredPhones.forEach(s->System.out.println(s));
    }
}
```

Выражение `HashSet::new` представляет функцию создания коллекции. Аналогичным образом можно получать другие коллекции, например, ArrayList:

```Java
ArrayList<String> result = phones.collect(Collectors.toCollection(ArrayList::new));
```

Вторая форма метода collect имеет три параметра:

```Java
<R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)
```

- `supplier`: создает объект коллекции
- `accumulator`: добавляет элемент в коллекцию
- `combiner`: бинарная функция, которая объединяет два объекта

Применим эту версию метода collect:

```Java
import java.util.ArrayList;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Program {
    public static void main(String[] args) {
        Stream<String> phones = Stream.of("iPhone 8", "HTC U12", "Huawei Nexus 6P",
                "Samsung Galaxy S9", "LG G6", "Xiaomi MI6", "ASUS Zenfone 2",
                "Sony Xperia Z5", "Meizu Pro 6", "Lenovo S850");

        ArrayList<String> filteredPhones = phones.filter(s->s.length()<12)
            .collect(
                ()->new ArrayList<String>(), // создаем ArrayList
                (list, item)->list.add(item), // добавляем в список элемент
                (list1, list2)-> list1.addAll(list2)); // добавляем в список другой список
        filteredPhones.forEach(s->System.out.println(s));
    }
}
```