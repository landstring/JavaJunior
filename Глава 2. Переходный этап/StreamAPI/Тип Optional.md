Ряд операций сведения, такие как min, max, reduce, возвращают объект Optional\<T>. Этот объект фактически обертывает результат операции. После выполнения операции с помощью метода get() объекта Optional мы можем получить его значение:

```Java
import java.util.Optional;
import java.util.ArrayList;
import java.util.Arrays;

public class Program {
    public static void main(String[] args) {
        ArrayList<Integer> numbers = new ArrayList<Integer>();
        numbers.addAll(Arrays.asList(new Integer[]{1,2,3,4,5,6,7,8,9}));
        Optional<Integer> min = numbers.stream().min(Integer::compare);
        System.out.println(min.get());  // 1
    }
}
```

Но что, если поток не содержит вообще никаких данных:

```Java
// список numbers пустой
ArrayList<Integer> numbers = new ArrayList<Integer>();
Optional<Integer> min = numbers.stream().min(Integer::compare);
System.out.println(min.get());  // java.util.NoSuchElementException
```

В этом случае программа выдаст исключение java.util.NoSuchElementException. Что мы можем сделать, чтобы избежать выброса исключения? Для этого класс Optional предоставляет ряд методов.

Самой простой способ избежать подобной ситуации - это предварительная проверка наличия значения в Optional с помощью метода isPresent(). Он возвращает true, если значение присутствует в Optional, и false, если значение отсутствует:

```Java
ArrayList<Integer> numbers = new ArrayList<Integer>();
Optional<Integer> min = numbers.stream().min(Integer::compare);

if(min.isPresent()){
    System.out.println(min.get());
}
```

### orElse

Метод orElse() позволяет определить альтернативное значение, которое будет возвращаться, если Optional не получит из потока какого-нибудь значения:

```Java
// пустой список
ArrayList<Integer> numbers = new ArrayList<Integer>();
Optional<Integer> min = numbers.stream().min(Integer::compare);
System.out.println(min.orElse(-1)); // -1

// непустой список
numbers.addAll(Arrays.asList(new Integer[]{4,5,6,7,8,9}));
min = numbers.stream().min(Integer::compare);
System.out.println(min.orElse(-1)); // 4
```

### orElseGet

Метод orElseGet() позволяет задать функцию, которая будет возвращать значение по умолчанию:

```Java
import java.util.Optional;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Random;

public class Program {
    public static void main(String[] args) {
        ArrayList<Integer> numbers = new ArrayList<Integer>();
        Optional<Integer> min = numbers.stream().min(Integer::compare);
        Random rnd = new Random();
        System.out.println(min.orElseGet(()->rnd.nextInt(100)));
    }
}
```

В данном случае возвращаемое значение генерируется с помощью метода nextInt класса Random, который возвращает случайное число.

### orElseThrow

Еще один метод - orElseThrow позволяет сгенерировать исключение, если Optional не содержит значения:

```Java
ArrayList<Integer> numbers = new ArrayList<Integer>();
Optional<Integer> min = numbers.stream().min(Integer::compare);
// генеррация исключения IllegalStateException
System.out.println(min.orElseThrow(IllegalStateException::new));
```

### Обработка полученного значения

Метод ifPresent() определяет действия со значением в Optional, если значение имеется:

```Java
ArrayList<Integer> numbers = new ArrayList<Integer>();
numbers.addAll(Arrays.asList(new Integer[]{4,5,6,7,8,9}));
Optional<Integer> min = numbers.stream().min(Integer::compare);
min.ifPresent(v->System.out.println(v)); // 4
```

В метод ifPresent передается функция, которая принимает один параметр - значение из Optional. В данном случае полученное минимальное число выводится на консоль. Но если бы массив numbers был бы пустым, и соответственно Optional не сдержало бы никакого значения, то никакой ошибки бы не было.

Метод ifPresentOrElse() позволяет определить альтернативную логику на случай, если значение в Optional отсутствует:

```Java
ArrayList<Integer> numbers = new ArrayList<Integer>();
Optional<Integer> min = numbers.stream().min(Integer::compare);

min.ifPresentOrElse(
     v -> System.out.println(v),
    () -> System.out.println("Value not found")
);
```

В метод ifPresentOrElse передается две функции. Первая обрабатывает значение в Optional, если оно присутствует. Вторая функция представляет действия, которые выполняются, если значение в Optional отсутствует.