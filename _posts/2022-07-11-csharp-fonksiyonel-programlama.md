---
title: C# Fonksiyonel Programlama
author: selimgezer
date: 2022-07-11 11:33:00 +0800
categories: [Programlama, C#]
tags: [c#, programlama, fonksiyonel programlama, delegate, closure, action, func, predicate]
math: true
mermaid: true
# image:
#   path: 
#   width: 800
#   height: 500
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

## Bölüm 1 - Delegate (Temsilci) Nedir?
---

## Delegate Nedir?

C#’da bulunan methodların bellek adreslerini tutmak için kullanılan yapıya delegate(temsilci) denir.

### Delegate Nasıl Tanımlanır?

```cs
<Access Modifier(public/private vs.)> delegate <return Type> <temsilci adi> (parameters);
```

>**Not:** Burada dikkat etmemiz gereken nokta bu tanımın kendi oluşturduğumuz bir tür olmasıdır.
{: .prompt-tip }

```cs
//Örnek:  
public delegate void EkleDelegate(int a, int b);  
```

Bu örneği incelediğimizde tanımladığımız tür ile birlikte artık bu türden oluşturulan nesneler geriye herhangi bir şey döndürmeyen(<span style="color:red">void</span>) ve <span style="color:red">2 tane int</span> parametre alan methodlara işaret edebilecektir.

### Delegate Nasıl Başlatılır?

Temsilciyi kullanmak için ilk önce bir nesne oluşturmalı ve temsilcinin kullanacağı methodu parametre olarak iletilmesi gerekir.

```cs
EkleDelegate temsilcim = new EkleDelegate (methodName);

veya

EkleDelegate temsilcim= methodName;
```

### Delegate Nasıl Çağrılır?

Temsilciyi çalıştırmak için ilgili temsilci istenen parametrelerle çağrılır.

```cs
temsilcim(100, 50);

veya

temsilcim.Invoke(100,50);
```

## Delegate Türleri Nelerdir?

### Single Cast Delegate 
Tek bir methodu çağırmak için bir temsilci kullanılıyorsa buna single cast delegate veya unicast delegate denir.
İlk kısımda incelediğimiz örnek single cast delegate türüne bir örnektir.

### Multicast Delegate 

Birden fazla işlevi temsil eden delegatelere multicast delegate denir. 

-	Bu türdeki bir temsilci kendisine eklenen tüm methodları çağıracaktır.
-	Eklenen methodlar aynı şekilde method imzalarına sahip olması gerekir.
-	Multicast delegate çağrı listesindeki(**Invokation list**) methodların eklendikleri sırasıyla çağrılır.Temsilciye birden fazla method iletirken + ve – operatörlerinden yararlanılır.
-	**+**  method eklemek için kullanılır. (**System.Delegate Combine**)
-	**-**   method çıkarmak için kullanılır.(**System.Delegate Remove**)


```cs
public void ToplamaIslemi(int a,int b){
	Console.WriteLine(a+b);
}

public void CarpmaIslemi(int a,int b){
	Console.WriteLine(a*b);
}

public delegate void IslemDelegate(int a,int b);

Main(){
    IslemDelegate temsilci=new IslemDelegate(ToplamaIslemi);
    temsilci+= CarpmaIslemi;
    temsilci(4,5);
} 

//Çıktı: 9 20
```

Void dışında değer dönderen methodları temsil eden bir temsilciye birden fazla method eklendikten(multicast delegate)  sonra temsilci çağrıldığında hangi değer geriye return olacaktır?

-	Bu durumda temsilci kendisine eklenen son methodun geriye dönderdiği değeri return eder.
-	Eğer bütün yüklenen methodların return değerlerini elde etmek istiyorsak GetInvocationList methodundan yararlanılır.


```cs
public delegate int Hesapla(int a,int b);

public int ToplamaIslemi(int a1, int a2){
return a1 + a2;
}

public int CarpmaIslemi(int a1, int a2){
return a1 * a2;
}
```

```cs
main(){
    Hesapla temsilci=new Hesapla(ToplamaIslemi);
    temsilci+=CarpmaIslemi;
    Console.Write(temsilci(4,5));
}
//Çıktı: 20  //Son eklenen işlem olan çarpmanın sonucunu verir
```

```cs
main(){
    Hesapla temsilci = new Hesapla(ToplamaIslemi);
    temsilci += CarpmaIslemi;

    Delegate[] methodlar = temsilci.GetInvocationList();

    foreach(Delegate item in methodlar){
        int sonuc = item.DynamicInvoke(4,5);
        //veya
        int sonuc = ( (Hesapla) item).Invoke(4,5);
        Console.WriteLine(sonuc);
        }
    }
//Çıktı: 9 20 //eklenme sırasıyla toplama ve çarpma işlemi sonucu
```

## Generic delegate türleri nelerdir?

### Action Delegate
Bir veya daha fazla girdi parametresi alır ve **hiçbir şey döndürmeyen(void)** türdeki methodları işaret edebilen delegate türüdür. Farklı veya aynı türden max 16 parametre alabilir.

```cs
Action temsilci = methodName; 
temsilci();

Action<parametre1,parametre2 ,,,,n> temsilci = methodName;
temsilci(parameters);
```

### Func Delegate

Bir veya daha fazla giriş parametresi alır ve **1 çıkış parametresi** döndürür. **Son parametre dönüş değeri(return)** olarak kabul edilir. Farklı veya aynı türden max 16 parametre alabilir. 

>**Not:** Dönüş türü zorunludur ancak giriş parametresi zorunlu değildir.
{: .prompt-tip }

```cs
Func<params , Return Type> funcTemsilci;
```


```cs
//Geriye int döndüren ve hiçbir parametre almayan methodlara işaret eden temsilci örneği
	
Func<int> temsilci=methodName; 
int x=temsilci();
```

```cs
//Geriye string değer döndüren ve int tipinde bir değer alan methodlara işaret eden temsilci örneği

Func<int,string> temsilci=methodName; 
string x=temsilci(5);
```

### Predicate Delegate
1 giriş parametresi alan ve her zaman Boolean tipinde değer dönderen delegate türüdür.

```cs
Predicate<T> = Func<T, bool> diyebiliriz.
```

```cs
//int parametre alan ve geriye boolean değer dönderen methodları işaret eden temsilci örneği

Predicate<int> temsilci=methodName; 
bool x=temsilci(9);
```

## Anonymous Method/Delegate Nedir?
Adında da anlaşılacağı gibi, anonim bir method adı olmayan bir methodtur. C#’ daki anonim methodlar **delegate** anahtar kelimesi kullanılarak tanımalanabilir ve **bir delegate türü değişkenine** atılabilir.

Notlar;

-	Anonim yöntem delegate anahtar sözcüğü kullanılarak tanımlanabilir.
-	Anonim yöntem dış değişkenlere veya methodlara erişebilir.
-	Anonim yöntem parametre olarak iletilebilir.
-	Anonim yöntemde return type ın belirtilmesine gerek yoktur. Method gövdesinin içindeki return ifadesinden çıkarılır. 


```cs

public delegate void Yaz(string kelime);

main(){
	Yaz temsilci = delegate (string x){
	                    Console.Write(x);
                     };

	temsilci(“selim”);
}

//Çıktı: selim
```

## Delegate Covariance and Contravariance

```cs
// Örnek Classlar

public class Small                 
{ 

}

public class Big: Small
{

}

public class Bigger : Big
{ 
    
}

main(){
    Small smlCls1 = new Small();  //çalışır
    Small smlCls2 = new Big();  //çalışır
    Small smlCls3 = new Bigger(); //çalışır
    Big bigCls1   = new Bigger();  //çalışır
    Big bigCls2   = new Small();  //çalışmaz
}
```

> Yukarıdaki instance örnekleri incelendiğimizde temel sınıf türetilmiş sınıfların örneklerini kabul ederken türetilmiş sınıflar temel sınıf örneğini kabul etmemektedir.
{: .prompt-tip }

### Covariance
Covariance, bir temel sınıfın beklendiği yerde türetilmiş bir sınıf kullanılmasına izin verilmesidir. Covariance delegate, array, interface vb. yapılar ile uygulanabilir.

##### Delegate Covariance
Delegate methodlarının dönüş türünde(returnType) esnekliğe izin verir. 

```cs
class Hayvan{

}

class Cita:Hayvan{

} 

delegate Hayvan hayvanTemsilcisi();  

Hayvan hayvanFonk(){

}

Cita citaFonk(){
  
}

main(){
hayvanTemsilcisi temsilci = hayvanFonk;  // çalışır
hayvanTemsilcisi temsilci = citaFonk;    // çalışır
}
```

Main methodun içerisine baktığımızda temsilcinin return değeri hayvan sınıfından olmasına rağmen hem hayvan sınıfı dönderen bir methodu hem de cita sınıfı dönderen bir methodu işaret edebilmiştir. Buna delegate covariance denir.


Return değerleri için bu durum kabul edilsede maalesef aynı işlem parametrelerde mümkün olmamaktadır.


```cs
class Hayvan{

}

class Cita:Hayvan{

} 

delegate void hayvanTemsilcisi(Hayvan parametre);       

void hayvanFonk(Hayvan parametre){

}

void citaFonk(Cita parametre){

}

main(){
hayvanTemsilcisi temsilci = hayvanFonk;  // çalışır
hayvanTemsilcisi temsilci = citaFonk;    // çalışmaz
}
```

Yukarıdaki örneğin Main methodunu incelediğimizde temsilci Hayvan sınıfı tipinde parametre desteklediği için hayvanFonk methodunu işaret edebilirken, citaFonk methodunu işaret edememektedir. Genelde bu kısım kafa karıştırıcı gelmektedir. Çünkü Cita zaten Hayvan sınıfından türetilmişse nasıl Cita tipinde bir parametre alan methodu delegate kabul edemez gibi düşünülebilir. Fakat buradaki önemli kısım temsilcinin davranışıdır. Eğer Cita’yı kabul edecek olsa, Hayvan sınıfından türeyen örneğin Kaplan sınıfınıda geçebilmemize olanak sağlayacaktı. Bu şekilde delegate’e geçilecek methodun ne olacağı kısmı netliğini kaybederdi. Peki böyle bir davranış istersek ne yapmamız gerekiyor?  İşte tam da bu noktada diğer kavramımız olan delegate contravarince incelememiz gerekiyor.


### Delegate Contravariance
Bir temel sınıfın parametresine sahip bir methodun, türetilmiş bir sınıfın parametresini bekleyen temsilciye atanmasına izin verir.

```cs
class Hayvan{

}

class Cita:Hayvan{

} 

delegate void hayvanTemsilcisi(Cita parametre);      

void hayvanFonk(Hayvan parametre){

}

void citaFonk(Cita parametre){

}

main(){
hayvanTemsilcisi temsilci = hayvanFonk; // çalışır
hayvanTemsilcisi temsilci = citaFonk;   // çalışır
}
```
Bu örnekteki main methodu incelediğimizde temsilcimizin parametre tipi Cita olmasına rağmen hem hayvanFonk hem de citaFonk methodlarına işaret edebilmektedir. Bu güvenlidir. Burada şöyle düşünebilir temsilcinin parametresi olan Cita kendisine geçirilen methodun tipine (Hayvan) dolaylı olarak typecast edilir.

## Ekstra Bilgiler

Not 1: Delegate isimlendirirken ismin sonunu Handler ile birmek bir yazılım geleneğidir.
> public delegate void **BenimTemsilcimHandler**();
{: .prompt-tip }

Not 2: İçerisinde bir method işaret edilmemeş temsilciler çağrıldığında Örneğin: temsilci(5)
Bu durumda **NullReferenceException** hatası alınmaktadır. Bu duruma sebebiyet vermemek için temsilcinin **null** kontrolü yapılması daha sağlıklı olacaktır.

```cs
if(temsilci!=null){
 temsilci(5);
}

//veya

temsilci?.Invoke(5)
```

## Bölüm 2 - Event(Olay) Nedir?
Özelleştirilmiş temsilciler(delegate) gibi düşünülebilir. Örneğin bir butona tıklanması, farenin hareket etmesi gibi olaylar bir event oluştururlar.

### Event Nasıl Tanımlanır?
1.	Delegate tanımlayın.
2.	Event anahtar kelimesini kullanarak delegate türünde bir değişken bildirin.

```cs
public delegate void Hesapla();       //delegate tanımı
public event Hesapla IslemTamamlandi; //event tanımı
```

Event’ın bulunduğu sınıfa yayıncı(publisher), bu event’a kayıt olanların bulunduğu sınıflara ise abone(subscriber) sınıfı denilmektedir.Bir sınıf event’a abone olmak için **+=** operatörünü kullanmaktadır.Abonelikten ayrılmak için **-=** kullanılmaktadır.                                                    

>Delegatelerde bulunan **=** operatörü ise eventlerde kullanılmamaktadır. 
{: .prompt-tip }

Yukaridaki örnekte delegate’i tanımlamamız bize event’a abone olacak methodların geri bir şey döndermeyen (void) ve herhangi bir parametre almayacağını belirtmektedir.

### Event'e Abone İşlemi Nasıl Gerçekleştirilir?
> **IslemTamamlandi** **+=** **methodName**;

Bu şekilde ilgili event’a abone olunur.

### Event'i Çağırma Nasıl Gerçekleştirilir?
>**IslemTamamlandi()**; 

Görüldüğü gibi tanımladığımız event’i çağırarak event’a abone olan tüm methodlar yürütülecektir.


## EventHandler Nedir?
C# da olaylar için tanımlanan yerleşik delegatelerden birisidir. Bir event’i incelediğimizde 2 parametre içermelidir.

```cs
EventHandler(object sender,EventArgs e)
```

Bunlar;
-	Olayın kaynağı (sender)
-	Olay verileri (e)

Buradaki object tipinden sender event’i başlatan objeyi ikinci parametre olan EventArgs tipinden e ise  olay verilerini tutar. Eğer event olay verisi üretmezse ikinci parametre **EventArgs.Empty** değeridir. Eğer olay verisi içeriyorsa ikinci parametre **EventArgs’tan türetilen bir türdür.**

**EventHandler** olay verilerini içermeyen tüm event’ler için kullanılabilir. Olay verilerini içeren event’lar için **EventHandler\<TEventArgs>** delegate’i kulanılabilir.

Örneğimizi EventHandler delegate ile ifade edecek olursak artık Hesapla delegatine ihtiyacımız kalmamaktadır. Direk aşağıdaki şekilde tanımlanabilir.


```cs
public event EventHandler IslemTamamlandi; //event tanımı

IslemTamamlandi( this , EventArgs.Empty); //eventi çağırma

//Buradaki this çağıran nesneyi EventArgs.Empty ise herhangi bir olay verisi olmayacağını belirtmektedir.
```

Yukarıdaki örnekte olay verisi içermeyen bir örnek yaptık. Olay verisi içeren bir event tanımalamak istersek aşağıdaki yapıyı kullanarak yapabiliriz.


```cs
public event EventHandler<bool> IslemTamamlandi;
```
Bu şekilde bu event’a abone olacak methodların bir **bool değişken** içereceğini belirtmiş oluyoruz. Bu event’a çağrıyı ise aşağıdaki gibi güncelliyoruz.

```cs
IslemTamamlandi(this, bool variable); //event’i çağırma
```

Eğer olay verisi olarak birden fazla değer iletmek istersek **EventArgs sınıfından türetilen bir sınıf oluşturup** o sınıftan yararlanmamız gerekir.

> Örnek

```cs
class IslemTamamlandiArgs : EventArgs{

	public bool IslemSonucu;
	public DateTime IslemYapilmaZamani;
}
```
Artık bu sınıfı EventHandler’a belirterek kullanabiliriz.

```cs
public event EventHandler<IslemTamamlandiArgs> IslemTamamlandi;
```
Şimdi abone olacak sınıfımızda **IslemTamamlandıArgs** sınıfından bir nesne oluşturup değerlerini belirtip event methoduna iletebiliriz.

```cs
IslemTamamlandiArgs cokluData= new IslemTamamlandiArgs();
cokluData.IslemSonucu=true;
cokluData.IslemYapilmaZamani=DateTime.Now;
IslemTamamlandi(this, cokluData); //event’i çağırma
```

## Bölüm 3 - Lambda Expression (Lambda İfadeleri)

Tanım: Sadeleştirilmiş anonim methodlardır.

Örnek: Verilen değerin 2 katını dönderen methodu yazınız.

```cs
int iki_Kati(int deger)		
{
	return 2 * deger;
}
```
Sorunun cevabı için yukarıdaki gibi bir method yazacaktık.Peki bu methodu lambda ifadesi olarak nasıl yazabiliriz?


Bu soruyu cevaplarken tanımı tekrar hatılayalım sadeleştirilmiş <ins>**anonim methodlardır.**</ins> Altı çizili kısma dikkat edersek C# da anonim methodların delegateler yardımıyla oluşturulduğunu bir önceki bölümden hatırlayalım.	O zaman yukarıdaki methodu delegate ile bir anonim method haline getirelim.

```cs
delegate (int deger)
{
	return 2 * deger;
};
```
Yukaridaki kod bloğunda methodun anonim hali gösterilmektedir. Artık sırasıyla bu methodu lambda ifadesi haline getirelim.

Bu noktada anonim methodun **delegate** ifadesini kaldırıp, **süslü parantezleri** de lambda işlecine **=>** çevirirsek methodun yeni hali

```cs
(int deger) => return 2 * deger ;
```

Bu ifade 2 * deger ifadesi de tek bir ifade belirttiğinden **return** ifadesini de kaldırabiliriz. Bu bizi aşağıdaki sonuca götürecektir.

```cs
(int deger) => 2 * deger ;
```

Bu kısımda dilersek artık parametrenin tipini de belirtmeyebiliriz. Yani (int deger) kısmını sadece (deger) olarak değiştirebiliriz.

```cs
(deger) => 2 * deger ;
```

Son olarak (deger) tek bir parametre olduğu için ( ) parantezlerini ve sondaki noktalı virgülü  kaldırabiliriz. Ama birden fazla parametre gerektiği durumlarda parantez kullanmamız şarttır.

```cs
deger => 2 * deger 
```

Son haline baktığımızda artık ifadenin tek satırla ifade edilmesi mümkün olmuştur. Bu şekilde tek satırlı olan lambda ifadelerine **ifade lambdaları (expression lambdas)** , çok satırlı lambda ifadelerine ise **komut lambdaları (statement lambdas)** denir. 

> Komut Lambda Örneği

```cs
x => {
    if(x>5){
  	return true;
          }
    else{
	return false; 
           }
};
```
Bir parametresiz lambda ifadesi kullanmak için aşağıdaki yapılar kullanılabilir.


```cs
() => { // yapılacaklar };
//veya
() => birMethod();
```

> **Not:** Lambda ifadelerine sıklıkla başvurulan noktalardan birisi LINQ sorgularıdır.
{: .prompt-tip }


Bazı C# tanım örnekleri:

```cs
-	Action action = () => { Console.Write("Action Calisti."); };
        action();	 //Çıktı : Action Calisti.

-	Func<int,int> func = y => { return 10+y; };
        func(5); //Çıktı: 15

-	Func<string,int,string> func = (ad,no) => { return ad+no.ToString(); };
        Console.Write(func("selim",27)); //Çıktı: selim27
```


## Bölüm 4 - Closures(Kapanışlar)

Wikipedia tanımını inceleyecek olursak. “Bir kapatma(closure), sözlüksel ortama bağlı(lexical scope) serbest değişkenlere(free variables) sahip birinci sınıf bir işlevdir(first-class function).”

Şimdi tanımdaki kavramları inceleyelim.

**Birinci Sınıf İşlev(first-class function) :** Yine wikipedia’dan tanımına bakarsak “Bir programlama dili, işlevleri birinci sınıf vatandaşlar olarak ele alıyorsa, birinci sınıf işlevlere sahip olduğu söylenir.”

Bu ifade programlama dilinin;
-	İşlevlerini diğer işlevlere argüman olarak iletmeyi
-	İşlevleri diğer işlevlerden değerler olarak döndermeyi
-	İşlevleri değişkenlere atamayı
-	İşlevleri veri yapılarında depolamayı 

desteklediği anlamına gelir. Ve C# da **temsilciler(delegate)** kullanarak bu maddeleri gerçekleştirebilmekteyiz.

```cs
Action _myAction = delegate
{
    Console.WriteLine("Selim Gezer");
};
_myAction();  //Çıktı: Selim Gezer
```

Yukarıdaki örnek C#’ın birinci sınıf işlevleri destekleğini göstermektedir.

**Serbest Değişken (free variable) :** İşlevin parametresi veya işlevin yerel değişkeni olmayan bir işlevde  başvurulan değişkendir.

```cs
string memleket = "Gaziantep";

Action _myAction= delegate
{
    Console.WriteLine("Selim Gezer "+ memleket);
};
_myAction();  //Çıktı: Selim Gezer Gaziantep
```

Bu örneği incelediğimizde **_myAction** değişkenimizin dikkat ederseniz kendi parametresi olmayan ve yerel değişkeni olmayan **memleket** değişkenine başvurmaktadır. Bu bir serbest değişkendir. İster yerel olsun ister serbest değişken olsun ne oluyor yani denilebilir. Hemen tanımı hatırlayalım bu bir **kapatma(closure)** oluşturmaktadır.

Konuyu daha iyi için aşağıdaki örnekleri inceleyelim.

```cs
int sayi = 1;
Action _myAction= delegate
{
    Console.WriteLine("{0} + 1 = {1}", sayi, sayi + 1);
};
_myAction();  //Çıktı: 1 + 1 = 2
```
Örneği incelediğimizde sayi adlı bir değişken ve Action tipinde bir methodu işaret edecek bir değişken bulunmaktadır. Burada dikkat etmemiz gereken nokta _myAction değişkeni kendi kapsamında bulunmayan bir değişken olan sayi değişkenine erişmektedir. Tanım gereği bu bir closure oluşturmakta olduğunu söylemiştik.  Ve çıktı olarak 1+1’den 2 elde edilmiştir. Belki bu kısımda tam olarak ne yapılmak istendiğini anlamamış olabilirsiniz. Aynı örneği kapsam değiştirerek inceleyelim. 

```cs
class Program
{
    static Func<int,int> _myFunc;

    static void Main(string[] args)
    {
      DigerMethodum();
      Console.WriteLine("ilk çağrı:"+_myFunc(1));  //Çıktı: 1 + 2 = 3
      Console.WriteLine("ikinci çağrı:"+_myFunc(2));  //Çıktı: 2 + 3 = 5
    }
 
    private static void DigerMethodum()
    {
        int sayi = 1;
        _myFunc = (int deger) =>
        {
		sayi++;
		return deger+sayi;
        };
    }
```
Bu örneğe çok dikkat edelim. İlk baktığımızda global kapsamda bir Func<int,int> tipinde **_myFunc** adlı bir değişken bulunmaktadır. Main method içerisinde **DigerMethodum** çağrılmaktadır. Ve **DigerMethodum** içerisinde sayi değişkeni ve _myFunc’ın işaret edeceği method bulunmaktadır. Ve bu method ilk başta sayi++ diyerek sayinin değerini 1 arttırıp soransında kendisine gelen parametre olan deger ile sayi değişkenini toplayarak geriye döndermektedir.  **DigerMethodum** çağrısından sonra Main method içerisinde 2 defa **_myFunc** temsilcisinin işaret ettiği method çağrılmıştır.

Şimdi çıktıları inceleyelim. İlk <ins>_myFunc(1)</ins> çağrısı yapılmaktadır.

Sırasıyla inceleyelim.
-	sayi başlangıçta=1 ‘dir.
	sayi++ ile sayi artik = 2 olmuştur. Daha sonra 
    return deger+sayi ise 2 + 1 ‘den 3 olarak dönmektedir. Ve çıktı 3’tür. Buraya kadar herhangi bir şey gözlemlemedik. Sonuçlar çoğu geliştiricinin beklediği şekildedir. 

Şimdi 2. Çağrı olan <ins>_myFunc(2)</ins> inceleyelim.

- sayi başlangıçta=2’dir
  sayi++ ile sayi artık=3 olmuştur. Daha sonra
  return deger + sayi ise 2 + 3 ‘den 5 olarak dönmektedir.

Evet tam da burada bizi şaşırtan bir sonuç oluşmaktadır. Direk bakıldığında çıktının 4 olduğu söylenecektir. Neden. Sayi++ sayiyi 2 yapacaktır. Gelen değerde 2 o zaman 2+2 ‘den 4 olacaktır. Fakat dikkat ettiniz mi sayi son değerini koruyup kaldığı değeri arttırarak işlem yapmaktadır. Hemen akla şu soru gelmektedir. Ee çağırdığımız fonksiyonlar işleri bittikleri zaman temizlenmezlermi? EVET normalde öyle olması beklenir. Ama burada sayi değişkeni kapsamı dışında da yaşamaya devam etmektedir.

> C#’da bir yerel değişkenin ömrü ona başvuran tüm kapanışların ömrünü içerecek şekilde uzatılır.
{: .prompt-tip }

Ve temsilciye referansımız bulunduğu müddetçe de yaşamaya devam edecektir.
 İşte bu olay tam olarak closure(kapanış)’dur

Bu sihirli davranış için C# derleyicinin davranışı araştırılabilir. Şimdi başka ilginç örneklere geçelim. 

```cs
public delegate void SayiDelegate();
  main(){	
      SayiDelegate delegateObjesi = null;
      for (int i = 1; i <= 3; i++)
        {
           delegateObjesi += () => Console.WriteLine( i );
        }
      delegateObjesi();
     }
//Çıktı: 4 4 4 (3 tane 4 bastırmıştır.)
```
Bu çıktının sizi çok şaşırttığını biliyorum. Genelde yeni başlayan geliştiriciler bu çıktının 1 2 3 vereceğeni düşünmektedir. Aslında kısmen haklıdırlar fakat kaçırdıkları kavram closure’dur. Çünkü dikkat ederseniz for döngüsüyle her seferinde 
**delegateObjesi += () => Console.WriteLine( i );**
ile delegateObjesine işaret ettirilen lambda ifadesi i değişkenine başvurmuştur. Ama bu tanım gereği closure oluşturmaktadır. Çünkü i lambda fonksiyonun kendi parametresi veya yerel değişkeni değildir.
Peki çıktı nasıl oluştu ?
delegateObjesi(); çağrıldığında içerisindekiler InvocationList ‘ göre sırasıyla tetiklenecektir.Yani aşağıdaki şekilde.
```cs
ConsoleWriteLine(i);
ConsoleWriteLine(i);
ConsoleWriteLine(i);
``` 
Peki neden 

```cs
ConsoleWriteLine( 1 );
ConsoleWriteLine( 2 );
ConsoleWriteLine( 3 );
```
bu şekilde değil. 

**Çünkü C#’da kapanışlar(closure) değişkenlerin değerlerini değil referanslarını yakalarlar.(Aşağıdaki resmi inceleyiniz)** Zaten o yüzden çıktı olarakta 3 tane 4 elde etmekteyiz. 

<!-- ![](../assets/resimler/csharp_oop.png) ! -->

<img src="../../assets/img/post1/delegate.jpg" >

Peki nasıl olsaydı ilk düşündüğüz gibi çıktı olarak 1 2 3 bastırılırdı?


```cs
public delegate void SayiDelegate();
  Main(){	
      SayiDelegate delegateObjesi = null;
      for (int i = 1; i <= 3; i++)
        {
           int kopya=i;  
           delegateObjesi += () => Console.WriteLine( kopya );
        }
      delegateObjesi();
     }
//Çıktı: 1 2 3 
```
Evet örneğe sadece ekstra bir değişken ekleyerek(int kopya) istediğimiz sonucu elde etmekteyiz. Ee ne farkı var i ‘ yi başka değişkene attık diyebilirsiniz. Ama şuna dikkat etmekte fayda var az önceki for döngüsünde direk i ‘yi kullandığımızda i bir taneydi yani hep aynı adres üzerinde işlem yapılmaktaydı(Şekil 4.1). Fakat burda her iterasyonda yeni bir kopya değişkeni oluşturmakta ve yeni adrese  i ‘nin değeri atılmaktadır(Aşağıdaki resmi inceleyiniz.).

<img src="../../assets/img/post1/delegate2.jpg" >

Diğer bir örnek:

```cs
main() {
        int i = 0;
        Action increment = delegate { ++i; };
        Console.WriteLine(i);
       
        ++i;
        Console.WriteLine(i);

        increment();
        Console.WriteLine(i);

        ++i;
        Console.WriteLine(i);

        increment();
        Console.WriteLine(i);
    }

//Çıktı: 0 1 2 3 4
```

Diğer bir örnek:

```cs
var list = new int[] { 0, 1, 2, 3 }; //closure
var funcList  = new List< Func<int> >();
var funcList2 = new List< Func<int> >();

foreach (var item in list)
{
  funcList.Add(() => item);
}
funcList.ForEach(f => Console.WriteLine( f() ));   // Çözüm1
for (var i = 0; i < list.Length; i++)
{
  funcList2.Add(() => list[i]);
}
funcList2.ForEach(f => Console.WriteLine( f() ));  // Çözüm2

//Çıktı(Çözüm1) : 0 1 2 3 
//Çıktı(Çözüm2) : System.IndexOutOfRangeException
```

Bu örneğimiz Çözüm1 yazılı satırımız bize beklediğimiz çıktıyı yani 0 1 2 3 bastırmıştır. Fakat Çözüm2 yazılı satırımız IndexOutOfRange hatası döndermiştir? Neden ? Hemen üzerindeki for döngüsünü inceleyelim ne yapmakta? funcList2 listesine her bir iterasyonda () => list[i] şeklinde bir lambda ifade eklemektedir. Fakat önceki örneklerden hatırlayın i değişkenine direk başvurulmaktadır. Bu da bütün iterasyonda aynı adres üzerinde çalışacak demektir. Yani yine for döngüsü bittiğinde i=4 olmaktadır.  Sonraki satırda ise çağrıldığında 
Console.WriteLine(list[4]) şeklinde çağrılacağından list’in 4 indexli bir değeri olmadığından hata fırlatacaktır.


## Bölüm 5 - Kaynakça

- https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/

- https://www.tutorialsteacher.com/csharp/csharp-covariance-and-contravariance

- http://sonergonul.net/csharp-delegate-tipine-genel-bir-bakis/
 
- https://www.kazimcesur.com/c-covariance-ve-contravariance/
 
- https://codepureandsimple.com/covariance-and-contravariance-with-c-410fc4102a02
 
- https://www.topcoder.com/thrive/articles/Invariance

- https://dotnettutorials.net/lesson/delegates-csharp/
 
- https://www.infoworld.com/article/2996770/how-to-work-with-delegates-in-csharp.html
 
- https://www.gencayyildiz.com/blog/cta-delegatetemsilci-ve-eventolay-kullanimi/
 
- https://yasirkula.com/2019/10/29/unity-c-delegate-ve-eventler/
 
- https://www.yusufsezer.com.tr/csharp-delegate/

- https://yazilim.cevapsitesi.com/Makaleler/2/cSharp-lambda-ifadeleri-lambda-expressions

- https://docs.microsoft.com/en-us/dotnet/api/system.eventhandler?view=net-5.0 

- https://www.fibiler.com/Divisions/Ehil/Mecmua/Magazines/Articles/txt/html/article_DelegateAndEventInCSharp.html
 
- https://pvs-studio.com/en/blog/posts/csharp/0468/
 
- http://www.sumeyraakbiyik.com/delegete-nedir-nasil-kullanilir/
 
- http://www.blackwasp.co.uk/CSharpClosures2.aspx/
 
- https://www.simplethread.com/c-closures-explained
 
- http://www.macoratti.net/16/09/c_closu1.html
 
- https://medium.com/yaz%C4%B1l%C4%B1ma-dair/javascript-closures-8b5db7294941
 
- https://codingberry.com/dotnet/2020/02/02/csharp-closure.html
 
- http://en.wikipedia.org/wiki/Closure_(computer_science) . 

- https://stackoverflow.com/questions/271440/captured-variable-in-a-loop-in-c-sharp

- https://stackoverflow.com/questions/9591476/are-lambda-expressions-in-c-sharp-closures

- https://www.alanzucconi.com/2021/03/13/delegates-lambda-closures/