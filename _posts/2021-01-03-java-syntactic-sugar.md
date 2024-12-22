---
title: Java Syntactic Sugar (Söz Dizimsel Şeker)
author: selimgezer
date: 2021-01-03 11:13:00 +0300
categories: ["Programlama", "Java"]
tags: ["java", "syntax", "syntactic sugar", "söz dizimsel şeker", "programlama"]
# math: true
# mermaid: true
# image:
#   path: 
#   width: 800
#   height: 500
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

## Java Syntactic Sugar (Söz dizimsel şeker) Nedir?
---

Bir programlama dilinde söz dizimsel şeker, programcının işini kolaylaştıran kullanışlı bir söz dizimi olarak tanımlanabilir. Aşağıdaki farklı yapılarda kullanabildiğimiz söz dizimsel şekerleri inceleyeceğiz ve çoktan birçoğunu ismini bilmeden kullandığınızı farkedeceksiniz. 

> Örnekleri JAVA üzerinden incelesekte birçok dil bu söz dizimsel şekerlere sahiptir.

### Increment/Decrement
Toplama, çıkarma, çarpma vb. birçok işlem sonrası atama yaptığımız standart söz dizimini, söz dizimsel şeker sayesinde daha sade bir şekilde kullanabiliriz.
```java
public static void incrementDecrementSyntaxSugar() {
    int i;

    i = i + 1; //standart

    i += 1;    //syntax sugar
}
```
Burada i++ ile bu durumu karıştırmayın. i++ (post increment) operatorudur. Bu operator ifade edildiği satırda önce mevcutu ilgili değere atayıp sonra değeri arttırmaya yönelik bir yol izler.
```java
public static void postIncrement() {
   int i = 2;
   int x = i++; //burada x ve i 2 olacaktır.
   //bu satırda artık i 3'tür.
}
```

### If-Else Statement
Sıklıkla kullandığımız karşılaştırma ifademiz olan if-else yapısını söz dizimsel şekerli hali olan ternary karşılaştırma olarak tek satırda kullanma şansı yakalayabiliriz. 
```java
public static void ifElseStandart(int peopleCount) { //standart
    if (peopleCount >= 2) {
        System.out.println("Count 2 veya 2 den büyük.");
    } else {
        System.out.println("Count 2 den küçük.");
    }
}
// Söz dizimi : koşul ? doğru ise bu kısım : yanlış ise bu kısım;
public static void ifElseSyntaxSugar(int peopleCount) { //syntax sugar
   System.out.println(peopleCount >= 2 ? "Count 2 veya 2 den büyük." : "Count 2 den küçük."); // ternary condition olarakta adlandırılır.
}
```

### For Loop
Döngülerde de yine sıklıkla el alışkanlığı ile standart for yazma eğilimi gösterebiliriz. Fakat eğer indisler üzerinde bir işlem gerçekleştirmeyip sadece okuma(read) işlemi gerçekleştirecek isek hiç indise girmeden söz dizimsel şekerli hali foreach i kullanabiliriz.
```java
public static void forLoopStandart() { //standart
    int[] numbers = {1, 2, 3, 4, 5};
    for (int i = 0; i < numbers.length; i++) {
        System.out.println(numbers[i]);
    }
}

public static void forLoopSyntaxSugar() { //syntax sugar
    int[] numbers = {1, 2, 3, 4, 5};
    for (int number : numbers) { //foreach döngüsü olarakta adlandırılır. Ve dizinin uzunluğuna gerek kalmadan iterasyonu kolayca yapabiliriz.
        System.out.println(number);
    }
}
```

### String Concatenation
Çoğu durumda string birleştirme işlemi yaptığımız senaryolarla karşılaşabiliyoruz ve bu gibi durumlarda ilk akla gelen string ifadeleri toplamak(+) yönünde olabiliyor. Kısa metinlerde belki bu işimizi görebiliyor fakat metnimiz biraz uzunsa ve harici parametrelerle desteklenmek istendiğinde metin biranda + işaretleri içerisinde yüzmeye başlıyor ve hataya açık bir hale gelebiliyor. Bu noktada yine söz dizimsel şeker olarak kabul edebileceğimiz format kullanımı ile daha temiz bir görünüme kavuşabiliriz.
```java
public static void stringConcatStandart(String whoami, int times) { //standart
    String message = "Ben bir " + whoami + "im. Ve " + times + " kez ders verdim.";
    System.out.println(message);
}

public static void stringConcatSyntaxSugar(String whoami, int times) { //syntax sugar
    String message = String.format("Ben bir %sim. Ve %d kez ders verdim.", whoami, times);
    System.out.println(message);
}
```
Burada akla StringBuilder yapısada gelebilir fakat bu yapı görünümden ziyade daha çok ifadeyi manipule etmeyi ve performansı iyileştirmeyi hedefler.

### Try - Catch
Java 7 ile gelen try-with-resources, otomatik kaynak yönetimi (özellikle dosya işlemleri) için kullanılır ve koda daha temiz bir görünüm katar. Aşağıda da görebileceğimiz gibi standart kullanımda finally bloğunda okuma işlemi yapan kaynağımızı kapatmamız(close) gerekirken, diğer kullanımda bu bloğu hiç eklemeden hem kaynağı kapatmayı unutma durumunu ortadan kaldırıyoruz hem de daha sade bir görünüm elde ediyoruz.
```java
public static void tryFinallyStandart() throws IOException { //standart
    BufferedReader reader = null;
    try {
        reader = new BufferedReader(new FileReader("file.txt"));
        //Dosya işlemleri
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } finally {
        if (reader != null) {
            reader.close();
        }
    }
}

public static void tryFinallySyntaxSugar() throws IOException { //syntax sugar
    try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
        //Dosya işlemleri
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }
}
```

### Lambda
Java 8 ile gelen lambda ifadeleri, özellikle koleksiyonlar üzerinde işlem yaparken daha kısa ve anlaşılır kodlar yazılmasını sağlar. Aşağıdaki örnekte bir koleksiyonu sıralamak istiyoruz ve bunun için Collection.sort tan yararlanıyoruz. Sıralama işlemini hangi şarta göre yapacağını anlayabilmek için 2. parametre olarak bir karşılaştırıcı(comparator) beklemektedir. Örneğimizde bu parametre anonim sınıf yardımı ile geçilmiştir. Ve koda göz korkutucu bir hava katmıştır. Aynı yapıyı söz dizimsel şeker kabul edebileceğimiz lambda ile ifade edince bu havanın nasılda dağıldığını hemen görebiliriz.
```java
public static void lambdaStandart() { //standart
    List<String> names = Arrays.asList("Victoria", "Alice", "Bella");
    Collections.sort(names, new Comparator<String>() {
        @Override
        public int compare(String o1, String o2) {
            return o1.compareTo(o2);
        }
    });
    names.forEach(System.out::println);
}

public static void lambdaSyntaxSugar() { //syntax sugar
    List<String> names = Arrays.asList("Victoria", "Alice", "Bella");
    Collections.sort(names, (o1, o2) -> o1.compareTo(o2));
    names.forEach(System.out::println);
}
```

### Lambda Method Reference
Lambda ifadeleri için ok(->) işaretlerinden yararlanmaktayız. Bunlardan kurtulup daha sade bir görünüme kavuşmak için method referanslı halini kullanabiliriz.
```java
public static void lambdaMethodReferenceStandart() { //standart
    List<String> names = Arrays.asList("Victoria", "Alice", "Bella");
    names.forEach(name -> System.out.println(name));
}

public static void lambdaMethodReferenceSyntaxSugar() { //syntax sugar
    List<String> names = Arrays.asList("Victoria", "Alice", "Bella");
    names.forEach(System.out::println);
}
```

## Risk
> Her şeyin fazlasının zarar olduğunu bilip aşırıya kaçarak daha okunur olmasını beklerken daha okunamaz hale getirmeyelim. Aşağıda iç içe ternary ler kullanıldığında okunurluğun nasıl düştüğünü görebilirsiniz. Yine benzer şekilde diğer kullanımlarında da bunları gözeterek kullanmak bizler için daha yararlı olacaktır. 
{: .prompt-warning }
```java
public static void lowReadable() { //okunurluk düşük
    int a = 10, b = 20, c = 30;
    String result = (a > b) ? ((a > c) ? "a is greatest" : "c is greatest") : ((b > c) ? "b is greatest" : "c is greatest");
    System.out.println(result);
}

public static void highReadable() { //okunurluk yüksek
    int a = 10, b = 20, c = 30;
    String result;
    if (a > b && a > c) {
        result = "a is greatest";
    } else if (b > c) {
        result = "b is greatest";
    } else {
        result = "c is greatest";
    }
    System.out.println(result);
}
```

## Sonuç
Söz dizimsel şeker geliştiren kişiye daha az kod yazma, kodu daha okunabilir ve bakımı daha kolay hale getirme imkanı tanır. Ancak risk kısmında da bahsettiğimiz gibi kaş yaparken de göz çıkarmamaya dikkat etmemiz gerekir. Son olarakta unutulmamalıdır ki bu özellikler yalnızca yazım kolaylığı sağlamaktadır, dilin performansını ya da davranışını değiştirmemektedir.