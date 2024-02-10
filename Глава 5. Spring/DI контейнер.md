Решает следующую проблему. 

Представим, что Бог Java хочет создать солнечную систему. 

```Java 
package refl;  
  
import java.util.ArrayList;  
import java.util.List;  
  
public class SolarSystem {  
    Star star;  
    List<Planet> planets = new ArrayList<>();  
}
```

Для этого он придумал концепции звёзд и планет. Договорился с апостолами, что звезда в системе одна, а планет у него может быть сколько угодно. Поскольку рук у Бога на создание нескольких солнечных систем не хватит, он хочет перепоручить создание планет и звёзд апостолам. Но вот незадача! А как же сделать так, чтобы создавали планеты и звёзды апостолы в своих измерениях, но Бог мог на основе их в своём измерении создать солнечные системы. Сильной зависимостью называется то, что он сам бы создал все объекты. Но хочется же избежать этого. Да, время у Бога не ограничено, но он не хочет быть ответственен за создание объектов. 

Ясное дело, что перекидывать планеты из измерения в измерение нельзя - пострадает всё то, что есть на этих планетах, а звёзды вообще взорвутся. Поэтому апостолы договорились с Богом о том, что передадут ему специальные инструкции для создания объектов. Config файлы. 

В первый раз они прислали ему какие-то непонятные XML-файлы, но Бог покрутил у виска и сказал, что лень ему (в нашем выдуманном мире лень - не такой страшный грех, Богу можно) заниматься чтением файлов. И он сказал: "Я - Бог Java, и хочу, чтобы инструкции были на Java". 

В недоумении апостолы, не знающие заповедей рефлексии и аннотаций, не понимая, как же Бог будет это всё обрабатывать, выкатили ему свои Config файлы. Вот пример такого: 

```Java 
package refl;  
  
public class Config {  
    public static Planet createPlanet1(){  
        return new Planet("Earth");  
    }  
    public static Planet createPlanet2(){  
        return new Planet("Saturn");  
    }  
    public static Star createStar(){  
        return new Star(3);  
    }  
    public static void readBible(){  
        System.out.println("Читаем Библию");  
    }
}
```

Поразился Бог невнимательности апостолов о том, что читают Библию они попутно с созданием таких серьёзных вещей и сказал: "Ну вы бы хоть показали, где вы читаете что-то, а где мне объекты создаёте, эхххх, дурачьё"

И тут он молвил: "@Bean !!!". Вот та самая пометка для таких методов, которые создают объекты. 

Апостолы прислали исправленные файлы: 

```Java 
package refl;  
  
public class Config {  
    @Bean  
    public static Planet createPlanet1(){  
        return new Planet("Earth");  
    }    
    
    @Bean  
    public static Planet createPlanet2(){  
        return new Planet("Saturn");  
    }    
    
    @Bean  
    public static Star createStar(){  
        return new Star(3);  
    }  
    
    public static void readBible(){  
        System.out.println("Читаем Библию");  
    }
}
```

Для себя Бог создал специальный контейнер, в который будет кидать все созданные объекты и сделал его недоступным для Дьявола

```Java 
package refl;  
  
import java.lang.reflect.Method;  
import java.util.ArrayList;  
import java.util.List;  
  
public class Container {  
    private List<Object> objects = new ArrayList<>();  
  
    public void load (Class cl) throws Exception{  
        Method[] methods = cl.getDeclaredMethods();  
        for (Method m  : methods){  
            if (m.isAnnotationPresent(Bean.class)){  
                objects.add(m.invoke(null));  
            }        }    }  
    public List<Object> objectList(){  
        return new ArrayList<>(objects);  
    }
}
```

И ТОГДА ОН СОЗДАЛ СОЛНЕЧНУЮ СИСТЕМУУУУ!!! БОЛЬШОЙ ВЗРЫВ!!!!!!

```Java 
package refl;  
  
import java.util.List;  
  
public class Main {  
    public static void main(String[] args) throws Exception {  
        Container container = new Container();  
        container.load(Config.class);  
        List<Object> list = container.objectList();  
  
        SolarSystem solarSystem = new SolarSystem();  
        for (Object o : list) {  
            if (o instanceof Star) {  
                solarSystem.star = (Star) o;  
            } else {  
                solarSystem.planets.add((Planet) o);  
            }        
        }        
        System.out.println(solarSystem);  
    }
}
```