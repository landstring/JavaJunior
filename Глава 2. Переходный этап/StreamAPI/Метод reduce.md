Метод reduce выполняет терминальные операции сведения, возвращая некоторое значение - результат операции. Он имеет следующие формы:

```Java
Optional<T> reduce(BinaryOperator<T> accumulator)
T reduce(T identity, BinaryOperator<T> accumulator)
U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)
```

Первая форма возвращает результат в виде объекта Optional\<T>. Например, вычислим произведение набора чисел:

```Java
import java.util.stream.Stream;
import java.util.Optional;

public class Program {
    public static void main(String[] args) {
        Stream<Integer> numbersStream = Stream.of(1,2,3,4,5,6);
        Optional<Integer> result = numbersStream.reduce((x,y)->x*y);
        System.out.println(result.get()); // 720
    }
}
```

Объект `BinaryOperator<T>` представляет функцию, которая принимает два элемента и выполняет над ними некоторую операцию, возвращая результат. При этом метод `reduce` сохраняет результат и затем опять же применяет к этому результату и следующему элементу в наборе бинарную операцию. Фактически в данном случае мы получим результат, который будет равен: `n1 op n2 op n3 op n4 op n5 op n6`, где op - это операция (в данном случае умножения), а n1, n2, ... - элементы из потока.

Затем с помощью метода `get()` мы можем получить собственно результат вычислений: `result.get()`

Или еще один пример - объединение слов в предложение:

```Java
Stream<String> wordsStream = Stream.of("мама", "мыла", "раму");
Optional<String> sentence = wordsStream.reduce((x,y)->x + " " + y);
System.out.println(sentence.get());
```

Вторая версия метода `reduce()` принимает два параметра:

```Java
T reduce(T identity, BinaryOperator<T> accumulator)
```

Первый параметр - `T identity` - элемент, который предоставляет начальное значение для функции из второго параметра, а также предоставляет значение по умолчанию, если поток не имеет элементов.

Второй параметр - `BinaryOperator<T> accumulator`, как и первая форма метода reduce, представляет ассоциативную функцию, которая запускается для каждого элемента в потоке и принимает два параметра. Первый параметр представляет промежуточный результат функции, а второй параметр - следующий элемент в потоке. Фактически код этого метода будет равноценен следующей записи:

```Java
T result = identity;
for (T element : this stream)
    result = accumulator.apply(result, element)
return result;
```

То есть при первом вызове функция accumulator в качестве первого параметра принимает значение identity, а в качестве второго параметра - первый элемент потока. При втором вызове первым параметром служит результат первого вызова функции accumulator, а вторым параметром - второй элемент в потоке и так далее. Например:

```Java
Stream<Integer> numberStream = Stream.of(-4, 3, -2, 1);
int identity = 1;
int result = numberStream.reduce(identity, (x,y)->x * y);
System.out.println(result);  // 24
```

Фактически здесь выполняется следующая цепь операций: `identity op n1 op n2 op n3 op n4...`

В предыдущих примерах тип возвращаемых объектов совпадал с типом элементов, которые входят в поток. Однако это не всегда удобно. Возможно, мы захотим возвратить результат, тип которого отличается от типа объектов потока. Например, пусть у нас есть следующий класс Phone, представляющий телефон:

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

И мы хотим найти сумму цен тех телефонов, у которых цена меньше определенного значения. Для этого используем третью версию метода reduce:

```Java
Stream<Phone> phoneStream = Stream.of(new Phone("iPhone 6 S", 54000),
            new Phone("Lumia 950", 45000),
            new Phone("Samsung Galaxy S 6", 40000),
            new Phone("LG G 4", 32000));
int sum = phoneStream.reduce(0,
            (x,y)-> {
                    if(y.getPrice()<50000)
                        return x + y.getPrice();
                    else
                        return x + 0;
            },
            (x, y)->x+y);
System.out.println(sum); // 117000
```

Опять же здесь в качестве первого параметра идет значение по умолчанию - 0. Второй параметр производит бинарную операцию, которая получает промежуточное значение - суммарную цену текущего и предыдущего телефонов. Третий параметр представляет бинарную операцию, которая суммирует все промежуточные вычисления.