Начиная с версии Java 16 в язык была добавлена новая функциональность - Records (на русском нередко называют "записями"). Records представляют классы, которые предназначены для создания контейнеров неизменяемых данных. Кроме того, records позволяют упростить разработку, сократив объем кода.

Для определения классов record применяется ключевое слово record, после которого идет название и далее в круглых скобках список полей record:

```Java
record название (поле1, поле2,...полеN){
    // тело record
}
```

Рассмотрим следующий пример

```Java
import java.util.Objects;

public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom", 36);
        System.out.println(tom.toString());
    }
}

class Person {

    private final String name;
    private final int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    String name() { return name; }

    int age() { return age; }

    public boolean equals(Object o) {
        if (!(o instanceof Person)) return false;
        Person other = (Person) o;
        return other.name == name && other.age == age;
    }

    public int hashCode() {
        return Objects.hash(name, age);
    }

    public String toString() {
        return String.format("Person[name=%s, age=%d]", name, age);
    }
}
```

Здесь определен класс Person, который определяет две константы - name и age:

```Java
private final String name;
private final int age;
```

Их значения устанавливаются в конструкторе. Больше никак их установить мы не можем. Таким образом, после создания объекта Person они будут хранить неизменяемые данные.

Для получения значений name и age предусмотрены одноименные методы:

```Java
String name() { return name; }
int age() { return age; }
```

Кроме того, здесь переопределены унаследованные от класса Object методы `equals()`, `hashCode()` и `toString()`.

В методе `main` создаем один объект класса Person и выводит на консоль его текстовое представление:

```Java
Person tom = new Person("Tom", 36);
System.out.println(tom.toString());
```

В итоге консоль нам выведет:

![[Pasted image 20230825232331.png]]

Теперь посмотрим, что нам предлагают Records - определим record, которая будет полностью аналогична вышеопределенному классу:

```Java
public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom", 36);
        System.out.println(tom.toString());
    }
}

record Person(String name, int age) { }
```

Records определяются с помощью ключевого слова record, за которым следует название записи. Дальше идет список полей записи. То есть в данном случае определяется два поля - name и age. Причем по умолчанию все они будут приватными и иметь модификатор final.

Также будет создаваться конструктор с двумя параметрами name и age. А каждого поля автоматически будет создаваться одноименный общедоступный метод для получения значения это поля. Например, для поля `name` создается метод `name()`, который возвращает значение поля name.

И также автоматически будут создаваться методы `equals, hashCode и toString`. В общем, данная record будет полностью аналогична вышеопределенному классу, но при этом содержит гораздо меньше кода.

При необходимости мы можем вызывать все имеющиеся методы:

```Java
public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom",  36);
        System.out.println(tom.name());     // Tom
        System.out.println(tom.age());      // 36
        System.out.println(tom.hashCode());
        Person bob = new Person("Bob", 21);
        Person tomas = new Person("Tom", 36);
        System.out.println(tom.equals(bob));    // false
        System.out.println(tom.equals(tomas));  // true
    }
}

record Person(String name, int age){ }
```

### Конструктор record

В примере выше применялась форма record:

```Java
record Person(String name, int age) { }
```

которая фактически создавала конструктор:

```Java
Person(String name, int age) {
    this.name = name;
    this.age = age;
}
```

Этот конструктор называется каноническим. Он принимает параметры, которые называются также как и поля record, и передает полям значения соответствующих параметров.

Тем не менее при необходимости мы можем изменить логику конструктора. Так, мы можем переопределить логику конструктора. Например, что если при создании объекта будет передан не валидный возраст? Предусмотрим эту ситуацию, переопределив логику конструктора:

```Java
public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom", -116);
        System.out.println(tom.toString());
    }
}

record Person(String name, int age) {
    Person{
        if(age<1 || age > 110){
            age = 18;
        }
    }
}
```

В данном случае если передано не валидное значение, то применяем некоторое значение по умолчанию (число 18). В итоге фактически мы получим конструктор со следующим действием:

```Java
Person(String name, int age) {
    if(age<1 || age > 110){
        age = 18;
    }

    this.name = name;
    this.age = age;
}
```

В итоге программа выведет на консоль следующее:

![[Pasted image 20230825233230.png]]

Мы можем полностью переопределить канонический конструктор:

```Java
public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom",  36);
        System.out.println(tom.toString());
        System.out.println(tom.name());
    }
}

record Person(String name, int age) {
    Person(String name, int age){
        if(age < 0 || age > 120) age = 18;
        this.name = name;
        this.age = age;
    }
}
```

Также мы можем определять какие-то другие конструкторы, но все они должны вызывать канонический конструктор:

```Java
public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom", "Smith",  36);
        System.out.println(tom.toString());
    }
}

record Person(String name, int age) {
    Person(String firstName, String lastName, int age){

        this(firstName + " " + lastName, age);
    }
}
```

Здесь определен конструктор, который условно принимает имя, фамилию и возраст пользователя. Этот конструктор вызывает канонический конструктор, передавая ему значения для полей name и age: `this(firstName + " " + lastName, age)`.

Консольный вывод программы:

![[Pasted image 20230825233657.png]]

### Переопределение методов

Также мы можем переопределить методы, которые имеет record по умолчанию. А это методы `equals()`, `hashCode()` и `toString()` и методы, которые называются также, как и поля записи, и которые возвращают значения соответствующих полей.

Например, переопределим для записи Person методы `toString()` и `name()`:

```Java
public class Program{
    public static void main (String args[]){
        Person tom = new Person("Tom", 36);
        System.out.println(tom.toString());
        System.out.println(tom.name());
    }
}

record Person(String name, int age) {

    public String name() { return "Mister " + name; }

    public String toString() {
        return String.format("Person %s, Age: %d", name, age);
    }
}
```

Консольный вывод программ:

![[Pasted image 20230825233831.png]]

### Ограничения records

Следует учитывать, что мы не можем наследовать запись record от других классов. Также нельзя наследовать классы от records. Однако классы record могут реализовать интерфейсы. Кроме того, классы record не могут быть абстрактными.

В record нельзя явным образом определять нестатические поля и инициализаторы. Но можно определять статические переменные и инициализаторы, также как статические и нестатические методы:

```Java
record Person(String name, int age){
    static int minAge;

    static{
        minAge = 18;
        System.out.println("Static initializer");
    }
}
```

