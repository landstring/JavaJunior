Метод skip(long n) используется для пропуска n элементов. Этот метод возвращает новый поток, в котором пропущены первые n элементов.

Метод limit(long n) применяется для выборки первых n элементов потоков. Этот метод также возвращает модифицированный поток, в котором не более n элементов.

Зачастую эта пара методов используется вместе для создания эффекта постраничной навигации. Рассмотрим, как их применять:

```Java
Stream<String> phoneStream = Stream.of("iPhone 6 S", "Lumia 950", "Samsung Galaxy S 6", "LG G 4", "Nexus 7");

phoneStream.skip(1)
    .limit(2)
    .forEach(s->System.out.println(s));
```

В данном случае метод skip пропускает один первый элемент, а метод limit выбирает два следующих элемента. В итоге мы получим следующий консольный вывод:

![[Pasted image 20230912182506.png]]

Вполне может быть, что метод skip может принимать в качестве параметра число большее, чем количество элементов в потоке. В этом случае будут пропущены все элементы, а в результирующем потоке будет 0 элементов.

И если в метод limit передается число, большее, чем количество элементов, то просто выбираются все элементы потока.

Теперь рассмотрим, как создать постраничную навигацию:

```Java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.stream.*;
import java.util.Scanner;

public class Program {
    public static void main(String[] args) {
        List<String> phones = new ArrayList<String>();
        phones.addAll(Arrays.asList(new String[]
                {"iPhone 6 S", "Lumia 950", "Huawei Nexus 6P",
                "Samsung Galaxy S 6", "LG G 4", "Xiaomi MI 5",
                "ASUS Zenfone 2", "Sony Xperia Z5", "Meizu Pro 5",
                "Lenovo S 850"}));
        int pageSize = 3; // количество элементов на страницу
        Scanner scanner = new Scanner(System.in);
        while(true){
            System.out.println("Введите номер страницы: ");
            int page = scanner.nextInt();
            if(page<1) break; // если число меньше 1, выходим из цикла
            phones.stream().skip((page-1) * pageSize)
                .limit(pageSize)
                .forEach(s->System.out.println(s));
       }
    }
}
```

В данном случае у нас набор из 10 элементов. С помощью переменной pageSize определяем количество элементов на странице - 3. То есть у нас получится 4 страницы (на последней будет только один элемент).

В бесконечном цикле получаем номер страницы и выбираем только те элементы, которые находятся на указанной странице.

Теперь введем какие-нибудь номера страниц, например, 4 и 2:

![[Pasted image 20230912182613.png]]