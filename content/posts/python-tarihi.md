---
title: "Python Tarihi"
date: 2022-12-23T15:30:50+03:00
---

Şimdiye kadar sayıları inceledik ve Python'un _**dinamik yazma**_ modelini araştırdık. Derinlemesine çekirdek nesne turumuzdaki bir sonraki ana tür, metin ve bayt tabanlı bilgileri depolamak ve temsil etmek için kullanılan sıralı bir karakter koleksiyonu olan Python _**dizesidir.**_ Bölüm 4'te dizelere kısaca göz gezdirmiştik. Burada, daha önce atladığımız bazı ayrıntıları daha derinlemesine inceleyeceğiz.

## Bu Bölümün İçeriği

Başlamadan önce, bu bölümde nelere yer verilmeyeceğini açıklığa kavuşturmak istiyorum. Bölüm 4'te _**Unicode**_ dizeleri ve ASCII olmayan metinler üzerinde çalışmak için dosya araçlarını incelemiştik. İnternet alanında çalışan bazı programcılar için _**Unicode**_ önemli bir araçtır. Örneğin, Web sayfalarında, e-posta içeriğinde ve başlıklarında, FTP aktarımlarında, GUI API'lerinde, dizin araçlarında, _**HTML**_, _**XML**_ ve _**JSON**_ olarak görüntülenebilir.

Aynı zamanda, _**Unicode**_ yeni başlayan programcılar için zorlayıcı bir konu olabilir ve bugün çoğu Python programcısı hala bu konunun tamamına hakim olmadan işlemlerini gerçekleştiriyor. Bu kitap _**Unicode**_ konusunun çoğunu Bölüm 37'de açıklamaktadır. Bu bölüm dize temellerine odaklanmaktadır.

Kısacası, bu bölüm Python'daki dize konusunun sadece bir kısmını açıklamaktadır, çoğu betik dosyasının kullandığı ve çoğu programcı için yeterlidir. ASCII metnini işleyen ve hangi Python sürümünü kullandığınıza bakılmaksızın aynı şekilde çalışan temel _**str**_ dize türünü inceler. Bu kasıtlı olarak sınırlandırılmış kapsama rağmen, _**str**_ aynı zamanda Python 3.X'te _**Unicode**_ işlediğinden ve ayrıca _**unicode**_ türü Python 2.X'te _**str**_ ile neredeyse aynı şekilde çalıştığından, burada öğreneceğimiz her şey doğrudan _**Unicode**_ işleme için de geçerli olacaktır.

### Unicode: Kısa Hikayesi

_**Unicode**_'u önemseyen okuyucular için etkilerinin ve işaretçileri hızlı bir şekilde özetlemek gerekmektedir. Resmi bir açıdan; ASCII, _**Unicode**_ metnin basit bir formudur, ancak birçok olası kodlama ve alfabeden sadece biridir. Türkçe konuşulmayan kaynaklardan alınan metinler çok farklı harfler kullanabilir ve dosyalarda saklandığında çok farklı şekillerde kodlanabilirler.

Bölüm 4'te gördüğümüz gibi, Python bunu her biri için farklı dize nesne türleri ve dosya arayüzleri ile metin ve ikili veriler arasında ayrım yaparak ele alır. Bu destek Python sürümüne göre değişir.

* Python 3.X sürümlerinde üç dize türü vardır: _**Unicode**_ metni \(ASCII dahil\) için _**str**_ kullanılır, ikili veriler \(kodlanmış metin dahil\) için _**bayt**_ kullanılır ve _**bytearray**_ değiştirilebilir bir _**bayt**_ varyantıdır. Dosyalar iki modda çalışır; _**str**_ olarak içeriği temsil eden ve _**Unicode**_ kodlamaları uygulayan metin ve ham baytlarla ilgilenen ve veri çevirisi yapmayan ikili dosyalar.
* Python 2.X sürümlerinde, _**unicode**_ dizeleri _**Unicode**_ metnini temsil eder, _**str**_ dizeleri hem 8-bit metin hem de ikili verileri işler ve 2.6 sürümü ile birlikte, sonraki ve 3.X sürümlerinde kullanılan _**bytearray**_ arka bağlantısı olarak kullanılabilir. Normal dosyaların içeriği yalnızca _**str**_ olarak temsil edilen bayttır, ancak bir _**codecs**_ modülü _**Unicode**_ metin dosyalarını açar, kodlamaları işler ve içeriği _**unicode**_ nesneleri olarak temsil eder.

Bu sürüm farklılıklarına rağmen, _**Unicode**_'u önemsemeniz gerekiyorsa ve bunun nistepen küçük bir uzantı olduğunu göreceksiniz. Metin bellekte olduğunda, bu konuda çalışacağımız tüm temel bilgileri destekleyen bir Python karakter dizisi bu bölümün temel konusudur. Aslında, _**Unicode**_'un birincil ayrımı genellikle onu dosyalara ve dosyalardan taşımak için gereken çeviri \(**encoding**\) adımında gerçekleşmektedir. Bunun ötesinde, büyük ölçüde sadece dize işlemleme işlemleridir.

Yine de, çoğu programcı _**Unicode**_ ayrıntılarını önden kavramaya gerek duymadığından, çoğunu Bölüm 37'de açıkladım. Bu daha gelişmiş dize kavramları hakkında bilgi edinmeye hazır olduğunuzda, buradaki dize temelleri materyalini okuduktan sonra hem Bölüm 4'teki önizlemelerine hem de Bölüm 37'deki tam _**Unicode**_ ve _**bayt**_ açıklamalarını inceleyin.

Bu bölüm için temel dize türüne ve işlemlerine odaklanacağız. Burada çalışacağımız teknikler doğrudan Python'un araç setindeki daha gelişmiş dize türleri için de geçerlidir.

## Dize ile ilgili Temel Bilgiler

İşlevsel bir bakış açısından, metin veya bayt olarak kodlanabilecek neredeyse her şeyi temsil etmek için dizeler kullanılabilir. Metin bölümünde buna semboller ve kelimeler, belleğe yüklenen metin dosyalarının içeriği, İnternet adresleri, Python kaynak kodu vb. dahildir. Dizeler, medya dosyaları ve ağ aktarımları için kullanılan ham baytları ve uluslararasılaştırılmış programlarda kullanılan ASCII _**Unicode**_ olmayan metnin hem kodlanmış \(**encoded**\) hem de kodu çözülmüş \(**decoded**\) formlarını tutmak için de kullanılabilir.

Dizeleri başka dillerde de kullanmış olabilirsiniz. Python'un dizeleri C gibi dillerde karakter dizeleriyle aynı role sahiptir; ancak dizilerden biraz daha üst düzey bir araçtır. C'nin aksine, Python'da, dizeler güçlü bir işleme araçları setiyle birlikte gelir. Ayrıca C gibi dillerden farklı olarak, Python'un ayrı karakterler için ayrı bir türü yoktur; bunun yerine, yalnızca tek karakterli dizeler kullanırsınız.

Açıkça söylemek gerekirse, Python dizeleri değişmez diziler olarak kategorize edilir, yani içerdikleri karakterlerin soldan sağa konumsal bir sıraya sahip olduğu ve yerinde değiştirilemeyeceği anlamına gelir. Aslında, dizeler, burada çalışacağımız diziler adı verilen daha büyük nesne sınıfının ilk temsilcisidir. Bu bölümde sunulan dizi işlemlerine özellikle dikkat edin, çünkü daha sonra inceleyeceğimiz listeler ve _**tuples**_ gibi diğer dizi türlerinde aynı şekilde çalışırlar.

Tablo 7-1, bu bölümde ele alacağımız dizeler ve işlemlerini önizlemektedir. Boş dizeler, aralarında hiçbir şey olmayan bir çift tırnak işareti \(tek veya çift\) olarak yazılır ve dizeleri kodlamanın çeşitli yolları vardır. İşleme için dizeler, Dizeleri işlemek için; birleştirme, dilimleme \(bölümleri ayıklama\), dizin oluşturma \(indeks ile getirme\) ve benzeri gibi ifade işlemlerini destekler. Python, ifadelerin yanı sıra, dizelere özgü görevleri uygulayan bir dizi dize yöntemi ve desen eşleştirme gibi daha gelişmiş metin işleme görevleri için modüller sağlar. Bunların hepsini bölümün ilerleyen kısımlarında araştıracağız.

Tablo 7-1

| Operasyon | Yorumlama |
| :--- | :--- |
| S = '' | Boş dize |
| S = "spam's" | Çift tırnak, tek tırnak ile aynıdır |
| S = 's\np\ta\x00m' | Kaçış dizeleri |
| S = """...çoklu satır...""" | Üç tırnaklı dize bloğu |
| S = r'\temp\spam' | Ham dize \(Kaçış yok\) |
| B = b'sp\xc4m' | Bayt dizesi Python 2.6, 2.7 ve 3.X sürümlerinde |
| U = u'sp\u00c4m' | Unicode dizesi Python 2.X ve 3.3'ten sonraki sürümlerde |
| S1 + S2 | Birleştirme |
| S \* 3 | Tekrarlama |
| S\[i\] | İndeks |
| S\[i:j\] | Dilimleme |
| len\(S\) | Uzunluk |
| "a %s papağan" % tur | Dize biçimlendirme ifadesi |
| "a {0} papağan".format\(tur\) | Dize biçimlendirme metodu Python 2.6, 2.7 ve 3.X sürümlerinde |
| S.find\('pa'\) | Dize arama metodu |
| S.rstrip\(\) | Dize boşluk silme metodu |
| S.replace\('pa', 'xx'\) | Dize değiştirme metodu |
| S.split\(','\) | Dizeyi bölümleme |
| S.isdigit\(\) | İçerik testi |
| S.lower\(\) | İçeriği küçük harflere çevirme |
| S.endswith\('spam'\) | Bitiş testi |
| 'spam'.join\(strlist\) | Birleştirme, |
| S.encode\('latin-1'\) | Unicode kodlama \(encoding\) |
| B.decode\('utf8'\) | Unicode şifre çözme \(decoding\) |
| for x in S: print\(x\) | İterasyon |
| 'spam' in S | Üyelik kontrolü |
| \[c \* 2 for c in S\] | Üyelik kontrolü |
| map\(ord, S\) | Üyelik kontrolü |
| re.match\('sp\(.\*\)am', line\) | Desen eşleme |

Tablo 7-1'deki çekirdek dize araçlarının ötesinde, Python ayrıca standart kütüphanenin Bölüm 4 ve Bölüm 36'da tanıtılan _**re**_ \(**regular expression**\) modülü ve hatta daha yüksek seviye ile daha gelişmiş desen tabanlı dizi işlemeyi XML ayrıştırıcıları gibi metin işleme araçlarını destekler. Bu kitabın kapsamı yine de Tablo 7-1'de temsil edilen esaslara odaklanmıştır.

Temel bilgileri kapsamak için, bu bölüm dize değişmez formlarına ve dize ifadelerine genel bir bakışla başlar, ardından dize yöntemleri ve biçimlendirme gibi daha gelişmiş araçları incelemeye devam eder. Python birçok dize aracıyla birlikte gelir ve hepsine burada bakmak mümkün değildir; daha detaylı bilgi için Python kütüphanesi ve referans kitapları incelenebilir. Buradaki amacımız size temsili bir örnek vermek için yeterli sık kullanılan araçları araştırmaktır. Burada eylemde görmeyeceğimiz yöntemler, büyük ölçüde göreceğimiz yöntemlere benzemektedir.

## Dize Değişmez Değerleri

Genel olarak, Python'da dizelerin kullanımı oldukça kolaydır. Belki de dizeler hakkında en karmaşık şey, bunları kodunuza yazmanın birçok yolu olmasıdır.

* Tek Tırnak: 'spa"m'
* Çift Tırnak: "spa'm"
* Üçlü Tırnak: '''... spam ...''', """... spam ..."""
* Kaçış Dizileri: "s\tp\na\0m"
* Ham Dize: r"C:\new\test.spm"
* Python 3.X, 2.6 ve sonraki sürümlerde; bayt değişmezleri: b'sp\x01am'
* Python 2.X, 3.3 ve sonraki sürümlerde; Unicode değişmezleri: u'eggs\u0020spam'

Tek ve çift tırnaklı formlar en uygun olanlarıdır; diğerleri özel roller üstlenirler ve iki gelişmiş form hakkında daha fazla içeriğe 37. bölümde inceleyeceğiz. Sırayla diğer tüm seçeneklere hızlıca bir göz atalım.

### Tek ve Çift Tırnaklı Dizeler Aynıdır

Python dizelerinin etrafında, tek ve çift tırnak karakterleri birbirinin yerine kullanılabilir. Yani, dizgi değişmezleri iki tek veya iki çift tırnak içine alınabilir. İki form aynı şekilde çalışır ve aynı nesne türünü döndürür. Örneğin, kodlandıktan sonra aşağıdaki iki dize aynıdır.

```python
>>> 'python', "python"
('python', 'python')
```

Her ikisini de desteklemenin nedeni, bir ters eğik çizhi ile kaçmadan bir dizenin içine diğer çeşitliliğin bir alıntı karakterini gömmenize izin vermesidir. Tek tırnak karakteri çift tırnak karakterleri içine alınmış bir dizeye gömebilirsiniz, bunun tersi de geçerlidir.

```python
>>> 'python"un', "python'un"
('python"un', "python'un")
```

Bu kitap genellikle, dizeye tek bir tırnak işareti gömülü durumlar dışında, okunması marjinal olarak daha kolay oldukları için dizelerin etrafında tek tırnak kullanmayı tercih etmektedir. Bu tamamen öznel bir stil seçimidir, ancak Python dizeleri bu şekilde görüntüler ve çoğu Python programcısı bugün de aynı şeyi yapar, bu yüzden muhtemelen de yapmalısınız.

Burada virgülün önemli olduğunu unutmayın. Bu olmadan, Python bitişik dize değişmezlerini herhangi bir ifadede otomatik olarak birleştirir, ancak aralarında birleştirme işlemini açıkça çağırmak için aralarına bir operatör eklemek oldukça kolaydır.

```python
>>> baslik = "Guido " 'van' " Rossum" # Örtük birleştirme
>>> baslik
'Guido van Rossum'
```

Bu dizeler arasına virgül eklemek bir dizeye değil bir diziye neden olur. Ayrıca, tüm bu çıktılarda, Python'un bir tane """ katmadıkça dizeleri tek tırnak içinde yazdırdığını unutmayın. Gerekirse, alıntı karakterleri ters eğik çizgilerden kaçarak da gömebilirsiniz.

```python
>>> 'python\'un', "python\"un"
("python'un", 'python"un')
```

Nedenini anlamak için, kaçışların genel olarak nasıl çalıştığını bilmeniz gerekmektedir.

### Özel Karakterleri Gösteren Kaçış Dizileri

Bir önceki bölümde, son örnek; bir dizeyi içine ters eğik çizgi koyarak bir dizenin içine yerleştirmiştik. Bu, dizelerdeki genel bir deseni temsil eder: ters eğik çizgiler, kaçış dizileri olarak bilinen özel karakter kodlarını tanıtmak için kullanılır.

Kaçış dizileri, karakterleri klavyeye kolayca yazılamayan dizelere gömmemizi sağlar. **\** Karakteri ve dizi değişmezinde onu izleyen bir veya daha fazla karakter, sonuçta ortaya çıkan dize nesnesinde, çıkış dizisi tarafından belirtilen ikili değere sahip tek bir karakterle değiştirilir. Örneğin, yeni satır ve sekme yerleştiren beş karakterli bir dizedir.

```python
s = 'a\nb\tc'
```

İki karakter **\n** tek bir karakter anlamına gelir. Karakter kümenizdeki yeni satır karakterinin ikili \(ASCII'de karakter kodu 10\) değeridir. Benzer şekilde, **\t** dizisi sekme karakteri ile değiştirilir. Bu dizenin yazdırıldığında nasıl göründüğü, onu nasıl yazdırdığınıza bağlıdır. Etkileşimli yankı, özel karakterleri kaçış olarak gösterir, ancak bunun yerine yazdırma _**print**_ bunları yorumlar.

```python
>>> s = 'a\nb\tc'
>>> s
'a\nb\tc'
>>> print(s)
a
b       c
>>>
```

Bu dizede kaç gerçek karakter bulunduğundan tamamen emin olmak için yerleşik _**len**_ işlevini kullanın; nasıl kodlandığına veya görüntülendiğine bakılmaksızın bir dizedeki gerçek karakter sayısını döndürür.

```python
>>> len(s)
5
```

Bu dize beş karakter uzunluğundadır: bir ASCII a, bir yeni satır karakteri, bir ASCII b vb. içerir.

Önceki sonuçtaki orijinal ters eğik çizgi karakterlerinin, dizedeki bellekle gerçekten saklanmadığını unutmayın; bunlar yalnızca dizede saklanacak özel karakter değerlerini tanımlamak için kullanılır. Bu tür özel karakterleri kodlamak için Python, Tablo 7-2'de listelenen tam bir kaçış kodu dizisi grubu tanır.

Tablo 7-2

| Kaçış | Anlamı |
| :--- | :--- |
| \newline | Yeni satır |
| \ | Ters eğik çizgi \(bir tane  kalır\) |
| \' | Tek tırnak |
| \" | Çift tırnak |
| \a | Zil kodu \(Bell karakteri\) |
| \b | Geri tuşu |
| \f | Form besleme |
| \n | Yeni satır |
| \r | Satırbaşı |
| \t | Yatay tab |
| \v | Dikey tab |
| \xhh | Hex değeri hh olan karakter \(tam olarak 2 basamak\) |
| \ooo | Sekizlik değeri ooo olan karakter \(3 basamağa kadar\) |
| \0 | Null, ikili 0 karakter \(dizeyi bitirmez\) |
| \N{ id } | Unicode veri tabanı ID |
| \uhhhh | 16 bit onaltılık değere sahip Unicode karakter |
| \Uhhhhhhhh | 32 bit onaltılık değere sahip Unicode karakter |
| \other | Kaçış yok \(hem  hem de diğerleri kalır\) |

{% hint style="info" %}
\Uhhhh. kaçış dizisi tam sekiz onaltılık basamak \(h\) alır; Hem "\u" hem de "\U" yalnızca 2.X'deki Unicode dize değişmelerinde tanınır, ancak 3.X'deki normal dizelerde \(Unicode olan\) kullanılabilir. 3.X baytlık bir değişmez değerde, onaltılık ve sekizli kaçışlar baytı verilen değerler belirtir; bir dize değişmezinde, bu kaçışlar; verilen kod noktası değerine sahip bir Unicode karakterini belirtir.
{% endhint %}

Bazı kaçış dizileri mutlak ikili değerleri bir dizenin karakterlerine gömmenizi sağlar. Örneğin, ikili sıfır değerleri \(bir basamaktan sekizlik kaçışlar olarak kodlanmış\) iki karakter ekleyen beş karakterli bir dize tanımlıyoruz.

```python
>>> s = 'a\0b\0c'
>>> s
'a\x00b\x00c'
>>> len(s)
5
```

Python'da, bunun gibi sıfır \(**null**\) bir karakter, bir dizeyi genellikle "**null byte**"'ın C'deki gibi sonlandırmaz. Bunun yerine Python, dizenin uzunluğunu ve metnini bellekte tutar. Aslında, Python'da bir karakter dizgiyi sonlandırmaz. İşte tüm mutlak ikili kaçış kodları olan bir dize: Bir ikili 1 ve 2 \(sekizli kodlanmış\), ardından bir ikili 3 \(onaltılık kodlanmış\).

```python
>>> s = '\001\002\x03'
>>> s
'\x01\x02\x03'
>>> len(s)
3
```

Python'un nasıl belirtildiklerine bakılmaksızın, yazdırılamayan karakterleri onaltılık olarak gösterdiğine dikkat edin. Mutlak değer kaçışlarını ve daha sembolik kaçış türleri Tablo 7-2'de serbestçe birleştirebilirsiniz. Aşağıdaki dize **"spam"** karakterlerini, bir sekmeyi, yeni satırı ve onaltılık olarak kodlanmış mutlak bir sıfır değeri karakteri içerir.

```python
>>> s = "s\tp\na\x00m"
>>> s
's\tp\na\x00m'
>>> len(s)
7
>>> print(s)
s       p
a m
```

Python'da ikili veri dosyalarını işlediğinizde bunu bilmek daha önemli hale gelir. İçerikleri betik dosyalarınızda dize olarak gösterildiğinden, her türlü ikili bayt değeri içeren ikili dosyaları işlemek sorun olmaz. İkili modlarda açıldığında, dosyalar harici dosyadan ham bayt dizeleri döndürür.

Son olarak, Tablo 7-2'deki son girişin gösterdiği, Python **\**'dan sonra karakteri geçerli bir kaçış kodu olarak tanımazsa, sonuçtaki dizede ters eğik çizgiyi tutar.

```python
>>> x = "C:\py\code" # Kelimeyi tam olarak tutar ve \\ olarak görüntüler.
>>> x
'C:\\py\\code'
>>> len(x)
10
```

Ancak, Tablo 7-2'nin tamamını belleğe kaydedemediğiniz sürece, muhtemelen bu davranışa güvenmemelisiniz. Değişmez ters eğik çizgileri açıkça dizelerinizde tutulacak şekilde kodlamak için, bunları iki katına çıkarın veya ham dizeler kullanın; sonraki bölüm nasıl yapılacağını gösterir.

### Ham Dizeler Kaçışları Bastırır

Gördüğümüz gibi, kaçış dizileri özel karakter kodlarını dizelere gömmek için kullanışlıdır. Örneğin, sınıflardaki Python'u yeni öğrenenler, aşağıdaki gibi bir dosya adı bağımsız değişkenine sahip bir dosya açmaya çalışırken görmek şaşırtıcı derecede yaygındır.

```python
dosya = open('C:\new_code\test.dat', 'w')
```

Burada "C:\new\_code" dizininde "test.dat" adlı bir dosyanın açılacağı düşünülür. Buradaki sorun; "\n" yeni satır karakteri için alınır ve "\t" bir sekme ile değiştirilir. Aslında, çağrı genellikle; "C: \(yeni satır\) we\_code \(sekme\) est.dat" adlı bir dosyayı açmaya çalışır.

Bu sadece ham dizlerinde yararlı olduğu bir şeydir. Bir dize açılış işleminden hemen önce "**r**" harfi \(büyük veya küçük harf\) görünürse, kaçış mekanizmasını kapatır. Sonuçta Python'un ters eğik çizgilerinizi tam olarak yazdığınız gibi tutmasıdır. Bu nedenle, dosya adı sorununu gidermek için Windows'ta "**r**" harfini eklemeyi unutmayın.

```python
dosya = open(r'C:\new_code\test.dat', 'w')
```

Alternatif olarak, iki ters eğik çizgi bir ters eğik çizgi için gerçekten bir kaçış dizisi olduğundan, ters eğik çizgilerinizi iki katına çıkararak koruyabilirsiniz.

```python
dosya = open('C:\\new_code\\test.dat', 'w')
```

Aslında, Python'un kendisi bazen gömülü ters eğil çizgiler içeren dizeler yazdırdığında bu katlama düzenini kullanır.

```python
>>> dosya = r'C:\new_code\test.dat'
>>> dosya
'C:\\new_code\\test.dat'
>>> print(dosya)
C:\new_code\test.dat
>>> len(dosya)
20
```

Sayısal gösterimle olduğu gibi, etkileşimli bilgi istemindeki varsyılan biçim sonuçları kodlarmış gibi yazdırır ve bu nedenle çıktıdaki ters eğik çizgilerden kaçar. _**print**_ deyimi, her noktada yalnızca bir ters eğik çizgi olduğunu gösteren daha kullanıcı dostu bir biçim sağlar. Durumun bu olduğunu doğrulamak için, dizedeki karakter sayısını görüntü formatlarından bağımsız olarak döndüren yerleşik _**len**_ işlevinin sonucunu kontrol edebilirsiniz. _**print\(dosya\)**_ çıktısındaki karakterleri sayarsanız, ters eğik çizgi başına toplamda sadece 20 karakter olduğunu görürsünüz.

Windows'taki dizin yollarının yanı sıra, ham dizeler de normal ifadeler için yaygın olarak kullanılır. Ayrıca Python betik dosyalarının genellikle Windows ve Unix'teki dizin yollarında eğik çizgi kullanabileceğini unutmayın; çünkü Python yolları portatif olarak yorumlamaya çalışır. Yani dosyaları açarken de "C:/new\_code/test.dat" çalışır. Ancak, yerel Windows ters eğik çizgiler kullanarak yolları kodlarsanız ham dizeler yararlıdır.

### Üçlü Alıntılar; Çok Satırlı Kod Blok Dizeleri

Şimdiye kadar; tek tırnak, çift tırnak, kaçış ve ham dize çalışırken inceledik. Python ayrıca, çok satırlı metin verilerinin kodlanması için söz dizimsel bir kolaylık olan, bazen blok dize olarak adlandırılan üç tırnaklı dize değişmez biçimine sahiptir. Bu form üç tırnak \(tek veya çift\) ile başlar, bunu herhangi bir sayıda metin satırı izler ve onu açan aynı üçlü tırnak dizisiyle kapatılır. Dizenin metninde gömülü olan tek ve çift tırnaklar kaçabilir, ancak zorunlu değildir. Python, değişmezi başlatmak için kullanılan aynı türden üç kaçışsız tırnak göre kadar dize bitmez. Örneğin;

```python
>>> zen = """ The Zen of Python, by Tim Peters
...
... Beautiful is better than ugly.
... Explicit is better than implicit."""
>>> zen
' The Zen of Python, by Tim Peters\n\nBeautiful is better than ugly.\nExplicit is better than implicit.'
```

Bu dize üç satırdan oluşur. Bölüm 3'te öğrendiğimiz gibi, bazı arayüzlerde, etkileşimli bilgi istemi ... böyle devam stırlarında değişir; ancak IDLE sadece bir satırı düşürür; bu kitap her ik iformdaki listeleri gösterir, gerektiğinde ekstra sonuca ulaşmaya dikkat edin. Her iki durumda da, Python tüm üç tırnaklı metni tek bir çok satırlı dizede toplar ve kodunuzun satır sonları olan yerlere gömülü yeni satır karakterleri \(**"\n"**\) ekler. Kelimenin tam anlamıyla olduğu gibi, sonucun ikinci satırının önde gelen boşluklara sahip olduğuna, ancak üçüncüsünün olmadığına dikkat edin. Yazdığınız şey gerçekten elde ettiğiniz şeydir. Yeni satırların yorumlandığı dizeyi görmek için _**print**_ yerleşik işlevini kullanın.

```python
>>> print(zen)
 The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
```

Aslında, üç tırnaklı dizeler; kodunuzun sağında yorum yapmak isteyebileceğiniz tüm metinler de dahil olmak üzere ekteki tüm metinleri saklar. Bu yüzden bunu yapmayın; yorumlarınızı alıntılanan metnin üstüne veya altına koyun veya daha önce belirtilen bitişik dizelerin otomatik birleştirmesini, istenirse açık satırlarla ve satır aralıklarına izin vermek için çevreleyen parantezleri kullanın.

```python
>>> menu = """ekmek     # Birinci yorum satırı!
... yumurta     # İkinci yorum satırı!"""
>>> menu
'ekmek\t# Birinci yorum satırı!\nyumurta\t#İkinci yorum satırı!'
>>> menu = ("ekmek\n"   # Birinci yorum satırı!
... "yumurta\n"         # İkinci yorum satırı!
... )
>>> menu
'ekmek\nyumurta\n'
>>>
```

Üç tırnaklı dizeler, programınızda çok satırlı metne ihtiyacınız olduğunda yararlıdır; örneğin, çok satırlı hata mesajlarını veya _**HTML**_, _**XML**_ veya _**JSON**_ kodunu Python kaynak kodu dosyalarınıza gömmek için. Harici metin dosyalarına veya açık birleştirme ve yeni satır karakterlerine başvurmadan üçlü alıntı yaparak bu blokları dpğrudan betik dosyalarınıza gömebilirsiniz.

Üç tırnaklı dizeler ayrıca, dosyalarınızdaki belirli noktalarda göründüklerinde yorum olarak alınan dize değişmezleri olan belge dizeleri için de yaygın olarak kullanılır. Bunların üçlü alıntı blokları olması gerekmez, ancak genellikle çok satırlı yorumlara izin verilir.

Son olarak, üç tırnaklı dizeler bazen geliştirme sırasında kod satırlarını geçici olarak devre dışı bırakmak için "hileli hack"; yolu olarak kullanılır. Tamam, gerçekten hileli değil ve aslında bugün oldukça yaygın bir uygulamadır, ancak amaç bu değildir. Birkaç kod satırını kapatmak ve betik dosyanızı tekrar çalıştırmak istiyorsanız, bunların üstüne ve altına üç tırnak işareti koyun.

```python
X = 1
"""
import os # Bu kod geçici olarak devre dışı bırakılmıştır
print(os.getcwd())
"""
Y = 2
```

Bunun hileli hack olduğunu söyledim çünkü Python gerçekten bu şekilde devre dışı bırakılan kod satırlarından bir dize yapabilir, ancak bu muhtemelen performans açısından önemli değildir. Büyük kod bölümleri için, her satırdan önce karma işaretlerini \(\#\) manuel olarak eklemek ve daha sonra bunları kaldırmaktan daha kolaydır. Bu, özellikle Python, kodunu düzenleme desteği olmayan bir metin düzenleyicisi kullanıyorsanız doğrudur.

{% hint style="info" %}
Python'da pratiklik genellikle estetiği yener.
{% endhint %}

## Dize İşlemleri

Öğrendiğimiz ifadelerle bir dize oluşturduktan sonra, bu dize üzerinde birçok işlem gerçekleştirmek isteyeceksiniz. Bu bölüm ve sonraki ikisi, Python dilinde metin işleme araçlarının ilk satırı olan dize ifadelerini, yöntemlerini ve biçimlendirmeyi açıklayacaktır.

### Temel İşlemler

Daha önce Tablo 7-1'de listelenen temel dize işlemlerini göstermek için Python yorumlayıcısı ile etkileşime geçerek başlayalım. Dizeleri "+" işleci kullanarak birleştirebilir ve "\*" işlecini kullanarak tekrarlayabilirsiniz.

```python
>>> len('abc')  # Dize'nin uzunluğu
3
>>> 'abc' + 'def' # Dize birleştirme sonucu yeni bir dize oluşturur
'abcdef'
>>> 'ab' * 3    # Dize tekrarı
'ababab'
>>>
```

Burada _**len**_ yerleşik işlevi, bir dizenin \(veya uzunluğa sahip başka herhangi bir nesnenin\) uzunluğu döndürür. Resmi olarak, iki dize nesnesinin eklenmesi, işlenenlerin içerikleri birleştirilmiş olarak yeni bir dize nesnesi oluşturur. "\*" işleci ile tekrarlama ise; dizeyi kendisine birkaç kez bir dize eklemek gibidir. Her iki durumda da, Python keyfi olarak boyutlandırılmış dizeler oluşturmanıza izin verir; veri yapılarının boyutları da dahil olmak üzere Python'da herhangi bir şey önceden tahmin etmeye gerek yoktur; yalnızda gerektiği gibi dize nesneleri oluştursunuz ve Python'un temeldeki bellek alanını otomatik olarak yönetmesine izin verirsiniz.

Tekrarlama başlangıçta bira belirsiz görünebilir, ancak şaşırtıcı sayıda bağlamda kullanışlı olur. Örneğin; 80 adet tire çizgisi yazdırmak için 80'e kadar sayabilir veya Python'un sizin için saymasına izin verebilirsiniz.

```python
>>> print('---- 80 adet ----')  # Zor yol
>>> print('-' * 80) # Kolay yol
```

Operatör aşırı yüklemesinin burada işe yaradığına dikkat edin. sayıları kullanırken toplama ve çarpma yapan aynı "+" ve "\*" operatörlerini kullanıyoruz. Python doğru işlemi yapar; çünkü eklenen ve çarpılan nesnelerin türlerini bilir. Ancak dikkatli olun; Kurallar beklediğiniz kadar serbest değildir. Örneğin, Python sayıları ve dizeleri ifadelerde karıştırmanıza izin vermez: "'abc' + 9" işlemi 9'u otomatik olarak bir dizeye dönüştürmek yerine bir hata oluşturur.

```python
>>> 'abc' + 9
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
```

Tablo 7-1'deki son satırda gösterildiği gibi, eylemleri yineleyen ifadeler için _**for**_ döngülerindeki dizeleri yineleyebilir ve temelde bir arama olan ifade işleci _**in**_ ile hem karakterler hem de alt dizeler için üyeliği test edebilirsiniz. Alt dizeler için; _**in**_ bu bölümün sonraki bölümlerinde yer alan _**str.find\(\)**_ yöntemine çok benzer; ancak alt dizenin konumu yerine bir _**Boole**_ sonucu döndürür.

```python
>>> kisi = 'Guido'
>>> for c in kisi:
...     print(c, end=' ')       # Adım adım yazdırma
...
G u i d o
>>> "d" in kisi # Bulunuyor
True
>>> "z" in kisi # Bulunmuyor
False
>>> 'do' in 'Guido'     # Dize içinde dize arama
True
>>>
```

**For** döngüsü, bir dizideki ardışık öğelere \(buradaki örnekte, bir dize\) bir değişken atar ve her öğe için bir veya daha fazla ifade yürütür. Aslında, "_**c**_" değişkeni, dizenin karakterleri arasında ilerleyen bir imleç haline gelir. Bu ve bunun gibi yineleme araçlarını Tablo 7-1'de listelenen diğerlerini bu kitapta daha ayrıntılı olarak; özellikle bölüm 14 ve 20'de inceleyeceğiz.

### Dizinleme ve Dilimleme

Dizeler sıralı karakter koleksiyonları olarak tanımlandığından, bileşenlerine konuma göre erişebilirsiniz. Python'da, bir dizedeki karakterler dizine ekleyerek getirilir; dizeden sonra köşeli parantez içinde istenen bileşenin sayısal uzaklığını sağlar. Bir karakter dizesini belirtilen konumda geri alırsınız.

C dilinde olduğu gibi, Python indeksleri 0'dan başlar ve dizenin uzunluğundan bir azında sona erer. Bununla birlikte, C'den farklı olarak Python, negatif indeksler kullanarak dizeler gibi dizilerden öğeler almanıza izin verir. Teknik olarak, pozitif bir indeks elde etmek için bir dizenin uzunluğuna negatif bir indeks eklenir. Negatif indeksleri sondan geriye doğru saymak olarak da düşünebiliriz.

```python
>>> s = 'spam'
>>> s[0], s[-2]   # İlk ve son indeksler
('s', 'a')
>>> s[1:3], s[1:], s[:-1] # Dilimleme ve bölme çıkarma
('pa', 'pam', 'spa')
>>>
```

İlk satır dört karakterli bir dize tanımlar ve ona "s" adını atar. Sonraki satır onu iki şekilde dizine ekler; s\[0\] önden 0'daki öğeyi soldan alır; tek karakterli dize 's'; s\[-2\] sondan 2'deki öğesi sondan geri veya eşdeğer olarak öteleme \(4 \(-2\)\) olarak tanımlanır. Daha grafik terimlerle, indeksler ve dilimler Şekil 7-1'de gösterildiği gibi hücrelere eşlenir.

![&#x15E;ekil 7-1](../.gitbook/assets/indeksleme-dilimleme.png)

Şekil 7-1. İndeksleme ve dilimleme: pozitif indeksler sol uçtan başlar \(indeks 0 ilk öğedir\) ve negatifler sağ uçtan geri sayar \(indeks -1 son öğedir\). Her iki indeks de indeksleme ve dilimleme işlemlerinde konum vermek için kullanılabilir.

Önceki örnekteki son satır, tek bir öğeyi değil, bölümün tamamını döndüren genelleştirilmiş bir dizin oluşturma biçimi olan dilimlemeyi gösterir. Muhtemelen dilimlemeyi düşünmenin en iyi yolu, özellikle dizelere uygulandığında bir tür ayrıştırma \(analiz yapısı\) olmasıdır. Tek bir adımda tüm bir bölümü \(alt dize\) çıkarmamıza izin verir. Dilimler, veri sütunlarını ayıklamak, öndeki ve sondaki metni kesmek ve daha fazlası için kullanılabilir. Aslında, bu bölümün ilerleyen kısımlarında metin ayrıştırma bağlamında dilimlemeyi araştıracağız.

Dilimlemenin temelleri açıktır. İki nokta üst üste ile ayrılmış bir çift indekse dize gibi bir dizi nesnesini dizine eklediğinizde, Python, indeks çifti tarafından tanımlanan bitişik bölümü içeren yeni bir nesne döndürür. Sol indeks alt sınır \(dahil\) olarak alınır ve sağ üst \(dahil değildir\) sınırdır. Diğer bir deyişle, Python alt sınırdan üst sınıra kadar olan ancak üst sınırı içermeyen tüm öğeleri getirir ve getirilen öğeleri içeren yeni bir nesne döndürür. Atlanırsa, sol ve sağ sınırlar varsayılan olarak 0 ve dilimlediğiniz nesnenin uzunluğunu sınırlar.

Örneğin, az önce gördüğümüz örnekte; s\[1:3\] indeks 1 ile 2'deki öğeleri çıkarır; ikinci ve üçüncü öğeleri alır ve indeks 3'teki dördüncü öğeden önce durur. Sonraki, s\[1:\] ilk öğenin sonrasındaki tüm öğeleri alır. Belirtilmeyen üst sınır varsayılan olarak dizenin uzunluğuna ayarlanır. Son olarak, s\[:-1\] son öğe dışındaki tüm öğeleri getirir; alt sınır varsayılan olarak 0 olur ve -1 son öğeyi \(dahil olmadan\) belirtir.

Bu ilk bakışta kafa karıştırıcı görünebilir, ancak indeksleme ve dilimleme, detayını anladıktan sonra kullanmak için basit ve güçlü araçlardır. Unutmayın, bir dilimin etkilerinden emin değilseniz etkileşimli olarak deneyin. Bir sonraki bölümde, bir dilime atayarak \(dizeler gibi değişmezler için olmasa da\) başka bir nesnenin tüm bölümünü tek adımda değiştirmenin bile mümkün olduğunu göreceksiniz. Referans için detayların bir özeti:

**İndeksleme \(s\[i\]\), indekslerde bileşenleri getirir.**

* İlk öğe 0 indeksindedir.
* Negatif indeksler, uçtan veya sağdan geriye doğru saymak anlamına gelir.
* s\[0\] ilk öğeyi getirir.
* s\[-2\] sondan ikinci öğeyi getirir. s\[len\(s\) -2\] ile aynı işlevdedir.

**Dilimleme \(s\[i:j\]\) dizilerin bitişik bölümlerini çıkarır.**

* Üst sınır dahil değildir.
* Dilim sınırları varsayılan olarak 0 ve atlanırsa sıra uzunluğu olarak ayarlanır.
* s\[1:3\], indeksi 1'den başlayarak, 3'3 kadar olan öğeleri getirir. İndeksi 3 olan öğe getirilmez.
* s\[1:\] indeksi 1'den sıra uzunluğunun sonuna kadar olan öğeleri alır.
* s\[:3\] 0 indeksinden 3'e kadar olan ancak 3'ü içermeyen öğeleri getirir.
* s\[:-1\] 0 indeksinden son öğeye kadar olan ancak son öğeyi içermeyen öğeleri getirir.
* s\[:\] 0 ile son öğe arasındaki öğeleri getirir. Bu s'nin üst düzey bir kopyasını oluşturur.

**Genişletilmiş dilimleme \(s\[i:j:k\]\) 1 olarak varsayılan bir adım veya k değerinde adım değerini kabul eder.**

* Öğelerin atlanmasına ve tersine çevrilmesine izin verir; bunun için sonraki bölümü inceleyin.

Burada listelenen ikinci son madde işareti öğesi çok yaygın bir tekniktir: bir dizi nesnesinin aynı değerlere sahip tam üst düzey bir kopyasını oluşturur. Ancak farklı bir bellek parçasıdır. Bu, dizeler gibi değişmez nesneler için çok yararlı değildir, ancak listeler gibi yerinde değiştirilebilen nesneler için kullanışlıdır.

Sonraki bölümde, indeks \(köşeli parantez\) ile dizin oluşturmak için kullanılan sözdiziminin sözlükleri anahtarla dizinlemek için kullanıldığını göreceksiniz; operasyonlar aynı görünür ancak farklı yorumlara sahiptirler.

#### Genişletilmiş dilimleme: Üçüncü sınır ve dilimleme nesneleri

Python 2.3 sürümü ve sonraki sürümlerinde, dilimleme ifadeleri, adım olarak kullanılan isteğe bağlı bir üçüncü parametre desteğine sahiptir. Adım, çıkarılan her öğenin dizinine eklenir. Bir dilimin tam şişmiş şekli x\[i:j:k\]'dır. Bu da "x'deki tüm öğeleri i'den j-1'e kadar k aralıklarıyla getir" anlamına gelir. Üçüncü sınır "_**k**_", varsayılan olarak 1'dir; bu nedenle normalde bir dilimdeki tüm öğeler soldan sağa çıkarılır. Ancak, açıkça bir değer belirtirseniz, öğeleri atlamak veya dizeyi tersine çevirmek için üçüncü sınırı kullanabilirsiniz.

Örneği; x\[1:10:2\], x'deki indeks numaraları 1 ve 9 arasındaki tüm öğeleri alır; yani indeks değerleri 1, 3, 5, 7 ve 9 olan öğeleri toplayacaktır. Her zamanki gibi, ilk ve ikinci sınırlar sırasıyla 0 ve dizinin uzunluğunu varsayılan olarak alır. Böylece x\[::2\] her dizenin başından sonuna kadar olan öğeleri bir atlayarak geri döndürür.

```python
>>> alfabe = 'abcdefghijklmnopqrstuvwxyz'
>>> alfabe[1:10:2]
'bdfhj'
>>> alfabe[::2]
'acegikmoqsuwy'
>>>
```

Ayrıca, öğeleri ters sırada toplamak için negatif bir adım kullanabilirsiniz. Örneğin; "merhaba\[::-1\]" dilimleme ifadesi yeni bir dizeyi "abahrem" döndürür. İlk iki sınır varsayılan olarak 0 ve dizinin uzunluğu gibi değerleri alır ve -1 adımının dilimin normal soldan sağa yerine sağdan sola gitmesini sağlar. Bu nedenle diziyi ters şekilde döndürür.

```python
>>> s = 'merhaba'
>>> s[::-1]
'abahrem'
>>>
```

Negatif bir adımla, ilk iki sınırın anlamları aslında tersine çevrilir. Yani; s\[5:1:-1\] dilimi, 2'den 5'e kadar olan öğeleri ters sırayla getirir. Sonuçta 5, 4, 3 ve 2 indekslerine sahip öğeleri içerir.

```python
>>> alfabe = 'abcdefghijklmnopqrstuvwxyz'
>>> alfabe[5:1:-1]
'fedc'
```

Bu şekilde atlama ve tersine çevirme, üç sınır dilimlerinin en yaygın kullanım durumlarıdır, ancak daha fazla ayrıntı için Python'un standart kitaplık kılavuzuna bakın. _**For**_ döngüsü ifadesi ile birlikte bu kitapta daha sonra üç limitli dilimleri tekrar gözden geçireceğiz.

Kitabın ilerleyen bölümlerinde dilimlemenin bir dilim nesnesiyle \(_**slice**_\) dizin oluşturmaya eşdeğer olduğunu ve her iki işlemi de desteklemek isteyen sınıflar için önemli bir bulgu olduğunu öğreneceğiz.

```python
>>> 'spam'[1:3] # Dilimleme sözdizimi
'pa'
>>> 'spam'[slice(1,3)]  # Slice nesnesi ve indeksi
'pa'
>>> 'spam'[::-1]
'maps'
>>> 'spam'[slice(None, None, -1)]
'maps'
>>>
```

### Dilimleme'ye neden önem vermelisiniz

Bu kitap boyunca, tanıtılan dil özelliklerinden bazılarının gerçek programlarda nasıl kullanıldığına bir göz atmak için bu bölümü okumaya devam edebilirsiniz. Python'da dilimlemeleri resmi bir şekilde görene kadar gerçekçi kullanım durumları anlayamayacağınız için, bu alt başlıkları henüz açıklanmamış konulara birçok referans içerebilir. Bu soyut dil kavramlarını ortak programlama görevleri için yararlı bulabileceğiniz yolların önizlemeleri olarak düşünebilirsiniz.

Örneğin, daha sonra bir Python programını başlatmak için kullanılan bir sistem komut satırında listelenen bağımsız değişken kelimelerinin yerleşik _**sys**_ modülünün _**argv**_ özniteliğinde kullanılabilir olduğunu göreceksiniz.

```python
# yazdir.py dosyasının içeriği
import sys
print(sys.argv)

# yazdir.py dosyasının çalıştırılması
python yazdir.py -a -b -c
['yazdir.py', '-a', '-b', '-c']
```

Genellikle, yalnızca program adını izleyen bağımsız değişkenleri incelemekle ilgilenirsiniz. Bu, tipik bir dilim uygulamasına yol açar: bir listenin ilk öğesi hariç tümünü döndürmek için tek bir dilim ifadesi kullanılabilir. Burada, _**sys.argv\[1:\]**_ istenen listeyi _**\['-a', '-b', '-c'\]**_ döndürür. Daha sonra bu listeyi ön taraftaki program adını barındırmak zorunda kalmadan işleyebilirsiniz.

Dilimler, genellikle giriş dosyalarından okunan satırları temizlemek için de kullanılırlar. Bir satırın sonunda bir satır sonu karakter \(bir **\n** satır sonu işareti\) olacağını biliyorsanız, _**satır\[:-1\]**_ gibi tek bir ifade ile satır sonu karakterinden kurtulabilirsiniz. Her iki durumda da, dilimler daha düşük seviyeli bir dilde açık olması gereken mantık işini yapar.

Bunu söyledikten sonra, _**satır.rstrip**_ yönteminin çağrılması genellikle satır sonu karakterlerini sıyırmak için tercih edilir, çünkü bu çağrı satır sonunda satır sonu karakteri yoksa satır bozulmadan kalır, bazı metin düzenleme araçlarıyla oluşturulan dosyalar için yaygın bir durumdur. Satırın doğru şekilde sonlandırıldığından eminseniz dilimleme çalışır.

## Dize Dönüştürme Araçları

Python'un tasarım sloganlarından biri, tahmin etmeyi ayarlamaları reddetmesidir. Temel bir örnek olarak, dize bir sayı gibi görünse bile Python'a sayı ve dize ekleyemezsiniz.

```python
# Python 3.X sürümlerinde
>>> "41" + 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str

# Python 2.X sürümlerinde
>>> "41" + 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot concatenate 'str' and 'int' objects
```

Bu tasarım gereğidir; hem toplama hem de birleştirme anlamına gelebileceğinden, dönüşüm seçimi belirsiz olacaktır. Bunun yerine Python bunu bir hata olarak ele alır. Python'da, yaşamınızı daha karmaşık hale getirecekse _sihir genellikle ihmal edilir._

Betik dosyanız bir dosyadan veya kullanıcı arabiriminden metin dizesi olarak bir sayı alırsa ne yapmalı? İşin püf noktası, bir dizeye sayı gibi davranmadan önce tam tersi dönüştürme araçları kullanmanızdır.

```python
>>> int('41'), str(41)  # Dizeden dönüştürme ve dizeye dönüştürme
(41, '41')
>>> repr(41)    # Kod dizesine dönüştürme
'41'
```

_**int**_ işlevi, bir dizeyi bir sayıya dönüştürür ve _**str**_ işlevi, bir sayıyı dize temsiline dönüştürür. _**repr**_ işlevi bir nesneyi dize temsiline dönüştürür, ancak nesneyi yeniden oluşturmak için yeniden çalıştırılabilen bir kod dizesi olarak döndürür. Dizeler için, sonuç Python satırları arasında formda farklılık gösteren bir _**print**_ ifadesiyle görüntüleniyorsa etrafında tırnaklar bulunur.

```python
>>> print(str('spam'), repr('spam'))
spam 'spam'
>>> str('spam'), repr('spam')
('spam', "'spam'")
```

Bu konularla ilgili daha fazla bilgi için bölüm 5'teki "str ve repr görüntü biçimleri" bölümü inceleyin. Bunlardan _**int**_ ve _**str**_ genellikle sayı ve dizi dönüştürme teknikleridir.

Şimdi, dizeleri ve sayı türlerini işlevlerin etrafında karıştırmasanız da, gerekirse bu işlemden önce işlenenleri manuel olarak dönüştürebilirsiniz.

```python
>>> s = '41'
>>> i = 1
>>> s + i
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
>>> int(s) + i      # Ekleme
42
>>> s + str(i)      # Birleştirme
'411'
```

Benzer yerleşik işlevler, dizelere ve dizelerden kayan nokta sayı dönüşümlerini işler.

```python
>>> str(3.1415), float('1.5')
('3.1415', 1.5)
>>> metin = '1.234E-10'
>>> float(metin)
1.234e-10
```

Daha sonra, yerleşik _**eval**_ işlevini daha fazla inceleyeceğiz; Python ifade kodunu içeren bir dize çalıştırır ve böylece bir dizeyi herhangi bir nesneye dönüştürebilir. _**int**_ ve _**float**_ işlevleri yalnızca sayılara dönüştürülür, ancak bu kısıtlama genellikle daha hızlı oldukları anlamına gelir. Ayrıca daha güvenlidir, çünkü rastgele ifade kodunu kabul etmezler. Bölüm 5'te kısaca gördüğümüz gibi, dize biçimlendirme ifadesi de sayıları dizelere dönüştürmek için bir yol sağlar. Biçimlendirmeyi bu bölümün ilerleyen kısımlarında ele alacağız.

### Karakter Kodu Dönüşümleri

Dönüşümler konusunda, tek bir karakteri, yerleşik _**ord**_ işlevine uygulanarak alttaki tam sayı koduna \(örneğin; ASCII bayt değeri\) dönüştürmek de mümkündür; bu, karşılık gelen simgeyi temsil etmek için kullanılan gerçek ikili karakter değeri döndürür. _**chr**_ işlevi, bir tam sayı kodu alıp karşılık gelen karaktere dönüştürerek ters işlemi gerçekleştirir.

```python
>>> ord('p')
112
>>> chr(112)
'p'
```

Teknik olarak, her ikisi de karakterleri sadece altta yatan karakter kümesinde tanımlayıcı sayıları olan _**Unicode**_ sıralarına veya "kod noktalarına" dönüştürür.

ASCII metni için bu, bellekteki tek bir bayta uyan tanıdık 7 bit tam sayıdır, ancak diğer _**Unicode**_ metin türleri için kod noktaları aralığı daha geniş olabilir. Gerekirse, bu işlevleri bir dizedeki tüm karakterlere uygulamak için bir döngü kullanabilirsiniz. Bu araçlar, bir tür dize tabanlı matematik gerçekleştirmek için de kullanılır. Örneğin, bir sonraki karaktere ilerlemek için matematiği tam sayıya dönüştürün ve yapın.

```python
>>> s = '5'
>>> s = chr(ord(s) + 1)
>>> s
'6'
>>> s = chr(ord(s) + 1)
>>> s
'7'
```

En azından tek karakterli dizeler için bu, dizeden tam sayıya dönüştürmek için yerleşik _**int**_ işlevini kullanmaya bir alternatif sağlar.

```python
>>> int('5')
5
>>> ord('5') - ord('1')
4
```

Bu tür dönüşümler, bir dize ikili basamak dizisini karşılık gelen tam sayı değerlerine dönüştürmek için Bölüm 4'te sunulan ve bu kitabın bir sonraki bölümünde derinlemesine ele alınan döngü ifadeleriyle birlikte kullanılabilir. Döngü boyunca her seferinde, geçerli değeri 2 ile çarpın ve sonraki basamağın tam sayı değerini inceleyin.

```python
>>> b = '1101'
>>> i = 0
>>> while b != '':
...     i = i * 2 + (ord(b[0]) - ord('0'))
...     b = b[1:]
...
>>> i
13
```

Sola kaydırma işlemi \(i &lt;&lt; 1\) burada 2 ile çarpmakla aynı etkiye sahip olacaktır. Yine de bu değişikliği önerilen döngüler olarak bırakacağız, çünkü hem henüz döngüleri ayrıntılı olarak incelemedik hem de Bölüm 5'te incelediğimiz _**int**_ ve _**bin**_ yerleşik işlevleri Python 2.6 ve 3.0 sürümlerinden itibaren ikili dönüşüm görevleri için destek sunmaktadır.

```python
>>> int('1101', 2)      # İkili'den tam sayıya dönüştürme
13
>>> bin(13)     # Tam sayıdan ikiliye dönüştürme
'0b1101'
```

{% hint style="info" %}
Yeterli zaman verildiğinde, Python en yaygın görevleri otomatikleştirme eğilimindedir.
{% endhint %}

### Dizeleri Değiştirme I

"Değişmez dizi" \(_immutable sequence_\) terimini hatırlıyor musunuz? Gördüğümüz gibi, değişme bölüm bir dizeyi yerinde değiştiremeyeceğiniz anlamına gelir.

```python
>>> s = 'spam'
>>> s[0] = 'x'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
>>>
```

_**Python'da metin bilgileri nasıl değiştirilir?**_ Bir dizeyi değiştirmek için, genellikle birleştirme ve dilimleme gibi araçları kullanarak yeni bir dize oluşturmanız ve atamanız gerekir, ardından istenirse sonucu dizenin orijinal adına geri atabilirsiniz.

```python
>>> s = s + 'SPAM!' # Dizeyi değiştirmek için yeni bir tane oluşturur
>>> s
'spamSPAM!'
>>> s = s[:4] + 'Python' + s[-1]
>>> s
'spamPython!'
>>>
```

İlk örnek, **s**'nin sonuna birleştirme ile bir alt dize ekler. Gerçekten, yeni bir dize oluşturur ve **s**'ye geri atar, ancak bunu orijinal dizeyi "değiştirmek" olarak düşünebilirsiniz. İkinci örnek dilimleme, dizine ekleme ve birleştirme yoluyla dört karakteri altı ile değiştirir. Bir sonraki bölümde göreceğiniz gibi, _**replace**_ gibi dize yöntemi çağrılarıyla benzer efektler elde edebilirsiniz.

```python
>>> s = 'splot'
>>> s = s.replace('pl', 'pamal')
>>> s
'spamalot'
```

Yeni bir dize değeri veren her işlem gibi, dize yöntemleri de yeni dize nesneleri oluşturur. Bu nesneleri saklamak istiyorsanız, değişken adlarına atayabilirsiniz. Her dize değişikliği için yeni bir dize nesnesi oluşturmak, göründüğü kadar verimsiz değildir; önceki bölümde açıklandığı gibi, Python eski kullanılmayan dize nesnelerini sildiğinizde otomatik olarak çöp olarak toplar, böylece önceki değerlerin tutulduğu alanlar daha yeni nesneler tarafından yeniden kullanılır. _**Python genellikle beklediğinizden daha verimlidir.**_

Son olarak, dize biçimlendirme ifadeleriyle yeni metin değerleri oluşturmak da mümkündür. Aşağıdaki her ikisi de nesneleri bir dizeye dönüştürür, bir anlamda nesneleri dizelere dönüştürür ve orijinal dizeyi biçim belirtimine göre değiştirir.

```python
>>> 'The %s Python' % ('Monty')         # Tüm Python sürümleri için; biçimlendirme ifadesi
'The Monty Python'
>>> 'The {0} {1}'.format('Monty', 'Python')     # Python 2.6, 2.7 ve 3.X sürümleri sonrası için
'The Monty Python'
```

**Değiştirme metaforuna rağmen, biçimlendirmenin sonucu değiştirilmiş bir nesne değil, yeni bir dize nesnesidir.** Biçimlendirmeyi bu bölümün ilerleyen kısımlarında inceleyeceğimiz; biçimlendirme bu örnekte belirtilenden daha genel ve yararlıdır. Önceki işlev çağrılarının ikincisi bir yöntem olarak sağlandığından, biçimlendirmeyi daha fazla araştırmadan önce dize yöntemi çağrılarını ele alalım.

## Dize Yöntemleri

İfade işlevlerine ek olarak, dizeler daha karmaşık metin işleme görevlerini gerçekleştiren bir dizi yöntem sağlar. Python'da ifadeler ve yerleşik işlevler bir dizi türde çalışabilir, ancak yöntemler genellikle nesne türlerine özgüdür; örneğin, dize yöntemleri yalnızca dize nesnelerinde çalışır. Bazı türlerin yöntem kümeleri Python 3.X'de kesişir, ancak yine de diğer araçlardan daha türe özgüdür.

### Yöntem Çağrı Söz dizimi

Bölüm 4'te incelendiği gibi, yöntemler sadece belirli nesnelerle ilişkili ve bunlarla ilgili işlevlerdir. Teknik olarak, her zaman gizli bir özneye ship çağrılabilir işlevlere başvuruda bulunan nesnelere eklenen özelliklerdir. Ayrıntılı olarak açıklarsak, işlevler kod paketleridir ve yöntem çağrıları aynı anda iki işlemi birleştirir: _**öznitelik getirme**_ ve _**yöntem çağrıları**_

* Özellik getirmeleri

_**nesne.attribute**_ formunun ifadesi, "nesnede özniteliğin değerini getir" anlamına gelir.

* Çağrı ifadeleri

For işlevinin \(bağımsız değişkenler\) bir ifadesi, "işlev kodunu çağırmak, virgülle ayrılmış sıfır veya daha bağımsız değişken nesnesini ona geçirmek ve işlevin sonuç değerini döndürmek" anlamına gelir.

Bu ikisini bir araya getirmek, bir nesnenin yöntemini çağırmanıza izin verir.

_**nesne.method\(argüman\)**_

Yöntem çağrısı ifadesi; soldan sağa değerlendirilir. Python önce nesnenin yöntemini alır ve sonra onu \(_**object**_\) çağırır, hem nesneyi hem de bağımsız değişkenleri \(_**arguments**_\) iletir. Düz ifadelerle, ifade çağrısı yöntemi şu anlama gelir.

{% hint style="info" %}
Nesneyi bağımsız değişkenlerle işlemek için yöntem çağrılır.
{% endhint %}

Yöntem bir sonuç hesaplarsa, tüm yöntem çağrısı ifadesinin sonucu olarak da geri döner. Daha somur bir örnek olarak;

```python
>>> s = 'spam'
>>> sonuc = s.find('pa')        # 'pa' değerini s dizesi içinde arayan dize yöntemi
>>> sonuc
1
```

Bu eşleme, hem yerleşik türlerin yöntemleri hem de daha sonra çalışacağımız kullanıcı tanımlı sınıflar için geçerlidir. Kitabın bu bölümünde göreceğiniz gibi, çoğu nesnede çağrılabilir yöntemler vardır ve hepsine aynı yöntem çağrısı sözdizimi kullanılarak erişilir. Bir nesne yöntemi çağırmak için, aşağıdaki bölümlerde göreceğiniz gibi, var olan bir nesneden çağırmanız gerekir; yöntemler nesne olmadan \(statik yöntemler hariç\) çalıştırılamaz.

### Dizelerin Yöntemleri

Tablo 7-3, Python 3.X sürümlerinde yerleşik dize nesneleri için yöntemleri ve çağrı modellerini özetlemektedir; bunlar sık sık değişir, bu nedenle en güncel liste için Python'un standart kitaplık kılavuzunu kontrol ettiğinizden veya herhangi bir dizede \(veya str türü adı\) etkileşimli olarak bir _**dir**_ veya _**help**_ çağrısı kullandığınızdan emin olun.

Python 2.X sürümlerinin dize yöntemleri biraz farklılık gösterir; örneğin, _**Unicode**_ verilerinin farklı işlenmesi nedeniyle bir kod çözme içerir. Bu tabloda, _**s**_ bir dize nesnesidir ve isteğe bağlı bağımsız değişkenler köşeli parantez içine alınır. Bu tablodaki dize yöntemleri, bölme ve birleştirme, büyük / küçük harf dönüşümleri, içerik testleri ve alt dize arama ve değiştirme gibi üst düzey işlemleri gerçekleştirir.

Tablo 7.3, Python 3.10 sürümünde dize yöntem çağrıları

| Dize Yöntem Çağrıları | Dize Yöntem Çağrıları |
| :--- | :--- |
| s.capitalize\(\) | s.join\(iterable\)\) |
| s.casefold\(\) | s.ljust\(width \[, fill\]\)\) |
| s.center\(width \[, fill\]\) | s.lower\(\) |
| s.count\(sub \[, start \[, end\]\]\)\) | s.lstrip\(\[chars\]\) |
| s.encode\(\[encoding \[,errors\]\]\) | s.maketrans\(x\[, y\[, z\]\]\) |
| s.endswith\(suffix \[, start \[, end\]\]\)\) | s.partition\(sep\) |
| s.expandtabs\(\[tabsize\]\) | s.replace\(old, new \[, count\]\)\) |
| s.find\(sub \[, start \[, end\]\]\)\) | s.rfind\(sub \[,start \[,end\]\]\) |
| s.format\(fmtstr, \*args, \*\*kwargs\)\) | s.rindex\(sub \[,start \[,end\]\]\)\) |
| s.format\_map\(\) | s.rjust\(width \[, fill\]\) |
| s.index\(sub \[, start \[, end\]\]\) | s.rpartition\(sep\) |
| s.isalnum\(\) | s.rsplit\(\[sep\[, maxsplit\]\]\) |
| s.isalpha\(\) | s.rstrip\(\[chars\]\) |
| s.isascii\(\) | s.split\(\[sep \[,maxsplit\]\]\) |
| s.isdecimal\(\) | s.splitlines\(\[keepends\]\) |
| s.isdigit\(\) | s.startswith\(prefix \[, start \[, end\]\]\) |
| s.isidentifier\(\) | s.strip\(\[chars\]\) |
| s.islower\(\) | s.swapcase\(\) |
| s.isnumeric\(\) | s.title\(\) |
| s.isprintable\(\) | s.translate\(map\) |
| s.isspace\(\) | s.upper\(\) |
| s.istitle\(\) | s.zfill\(width\) |
| s.isupper\(\) |  |

Gördüğünüz gibi, oldukça çok sayıda dize yöntemi var ve hepsini kapsayacak bir alanımız yok; tüm ince noktalar için Python'un kütüphane kılavuzuna veya referans metinlerine bakın. Bununla birlikte, başlamanıza yardımcı olmak için, en sık kullanılan yöntemlerden bazılarını gösteren ve Python metin işleme temellerini gösteren bir kod üzerinde çalışalım.

### Dize Yöntemi Örnekleri: Dizeleri Değiştirme II

Gördüğümüz gibi, dizeler değişmez olduğu için doğrudan yerinde değiştirilemezler. _**Bytearray**_ 2.6, 3.0 ve sonraki Python sürümlerinde yerinde metin değişikliklerini destekler, ancak yalnızca 8 bitlik basit türler için bu durum geçerlidir. Daha önce metin dizelerindeki değişiklikleri incelemiştik, ancak dize yöntemleri bağlamında bu konuya ikinci kez göz atacağız.

Genel olarak, varolan bir dizeden yeni bir metin değeri yapmak için; dilimleme ve birleştirme gibi işlemlerle yeni bir dize oluşturursunuz. Örneğin, bir dizenin ortasındaki iki karakteri değiştirmek için aşağıdaki gibi bir kod kullanabilirsiniz.

```python
>>> S = 'spammy'
>>> S = S[:3] + 'xx' + S[5:]
>>> S
'spaxxy'
```

Ancak, bir alt dizeyi gerçekten değiştirmek için dilimleme kullanıyorsanız, bunun yerine dize _**replace**_ yöntemini kullanabilirsiniz.

```python
>>> s = 'spammy'
>>> s = s.replace('mm', 'xx')
>>> s
'spaxxy'
```

_**replace**_ yöntemi, bu kodun ima ettiğinden daha geneldir. Değiştirilecek orijinal alt dizeyi \(herhangi bir uzunlukta\) ve dizeyi \(herhangi bir uzunlukta\) bağımsız değişken olarak alır ve genel bir arama ve değiştirme gerçekleştirir.

```python
>>> 'aa$bb$cc$dd'.replace('$', 'SPAM')
'aaSPAMbbSPAMccSPAMdd'
```

Böyle bir olde yer değiştirme, şablon değişikliklerini uygulamak için bir araç olarak kullanılır. Bu kez sonucu bir ada atamak yerinebasitçe yazdırdığımıza dikakt edin; sonuçları daha sonra kullanmak üzere saklamak istediğinizde adlara atamanız gerekir.

Herhangi bir indekste oluşabilecek sabit boyutlu bir dizeyi değiştirmeniz gerekirse, yeniden değiştirme yapılabilir veya alt dizeyi dize _**find**_ yöntemiyle arayabilir ve sonra dilimleyebilirsiniz.

```python
>>> s = 'xxxxSPAMxxxxSPAMxxxx'
>>> konum = s.find('SPAM') # Konum arama
>>> konum # 4'üncü indekste bulunuyor
4
>>> s = s[:konum] + 'EGGS' + s[(konum + 4):]
>>> s
'xxxxEGGSxxxxSPAMxxxx'
```

_**Find**_ yöntemi, alt dizenin göründüğü indeksi \(varsayılan olarak baştan arama\) veya bulunamazsa -1 değerini döndürür. Daha önce gördüğümüz gibi, tıpkı _**in**_ ifadesindeki gibi alt dize arama işlemidir; ancak _**find**_ yöntemi bulunan bir alt dizenin konumunu döndürür.

Başka bir seçenek, tek bir değişiklik ile sınırlamak için _**replace**_ yöntemine üçüncü bir argüman sağlamaktır.

```python
>>> S = 'xxxxSPAMxxxxSPAMxxxx'
>>> S.replace('SPAM', 'EGGS') # Tümünü değiştir
'xxxxEGGSxxxxEGGSxxxx'
>>> S.replace('SPAM', 'EGGS', 1) # Sadece ilk bulunanı değiştir
'xxxxEGGSxxxxSPAMxxxx'
```

_**replace**_ yönteminin her seferinde yeni bir dize nesnesi döndürdüğüne dikkat edin. Dizeler değişmez olduğu için, yöntemler "dizge" olarak adlandırılsalar bile konu dizelerini gerçekten değiştirmezler!

Birleştirme işlemlerinin ve _**replace**_ yönteminin, her çalıştırıldıklarında yeni dize nesneleri oluşturması, bunları dizeleri değiştirmek için kullanmanın potansiyel bir dezavantajıdır. Çok büyük bir dizeye birçok _**replace**_ yöntemi uygulamanız gerekiyorsa, dizeyi yerinde değişiklikleri destekleyen bir nesneye dönüştürerek betik dosyanızın performansını artırabilirsiniz.

```python
>>> s = 'spammy'
>>> l = list(s)
>>> l
['s', 'p', 'a', 'm', 'm', 'y']
~
```

Yerleşik _**list**_ işlevi \(bir nesne oluşturma çağrısı\) herhangi bir sırayla öğelerden yeni bir liste oluşturur. Bu durumda, bir dizenin karakterlerini bir listeye kopyalar. Dize bu formda olduğunda, her değişiklik için yeni bir kopya oluşturmadan dizede birden çok değişiklik yapabilirsiniz.

```python
>>> l[3] = 'x' # Listeler için çalışır, dizeler için çalışmaz
>>> l[4] = 'x'
>>> l
['s', 'p', 'a', 'x', 'x', 'y']
```

Değişikliklerinizden sonra, bir dizeye geri dönüştürmeniz gerekiyorsa, listeyi bir dizeye yerleştirmek için dize _**join**_ yöntemini kullanın.

```python
>>> s = ''.join(l)
>>> s
'spaxxy'
```

_**join**_ yöntemi ilk bakışta biraz geriye gidiş olarak gözükebilir. Bir dizge yöntemi olduğundan istenen sınırlayıcıdan çağrılır. _**join**_, dizeleri ,liste öğeleri arasındaki sınırlayıcılarla birlikte listeye bir araya getirir; bu durumda, listeden dizeye dönüştürmek için boş bir dize sınırlayıcısı kullanır. Daha genel olarak, herhangi bir dize sınırlayıcı ve dizge yinelenebilir.

```python
>>> 'Python'.join(['Guido', 'van', 'Rossum'])
'GuidoPythonvanPythonRossum'
```

Aslında, alt dizeleri bir kere birleştirmek, genellikle bunları tek tek birleştirmekten daha hızlı çalışabilir. Ayrıca bölüm 27'de tam olarak açıklanan Python 3.0 ve 2.6 sürümlerinden itibaren mevcut değişikliğe uğramış _**bytearray**_ dizesi ile ilgili daha önceki notu gördüğünüzden emin olun. Yerinde değiştirilebilmesi nedeni ile, sık sık değiştirilmesi gereken bazı 8 bit metin türleri için bu _**list**_ ve _**join**_ kombinasyonuna bir alternatif sunar.

### Dize Yöntemi Örnekleri: Metin Ayrıştırma

Dize yöntemleri için bir diğer yaygın rol, metin ayrıştırma işleminin basit bir biçimidir; yani yapıyı analiz etmek ve alt dizeleri çıkarmaktır. Sabit indekslerde alt dizeleri çıkarmak için dilimleme teknikleri kullanabiliriz.

```python
>>> satir = 'aaa bbb ccc'
>>> kolon1 = satir[0:3]
>>> kolon3 = satir[8:]
>>> kolon1
'aaa'
>>> kolon3
'ccc'
```

Burada, veri sütunları sabit indekslerde görünü ve bu nedenle orijinal dizeden dilimlenebilir. Bu teknik, verilerinizin bileşenleri sabit konumlara ship olduğu sürece ayrıştırma için geçerlidir. Bunun yerine bir çeşit ayırıcı veriyi ayırırsa, bileşenlerini bölerek çıkarabilirsiniz. Veriler dizede rastgele konumlarda görünse bile bu işe yarar.

```python
>>> satir = 'aaa bbb ccc'
>>> kolon = satir.split()
>>> kolon
['aaa', 'bbb', 'ccc']
```

Dize bölme yöntemi, bir dizeyi, bir sınırlayıcı dizenin etrafında bir alt dizeler listesine ayırır. Önceki örnekte bir sınırlayıcı kullanmadık, bu nedenle varsayılan olarak boşluk alanı kullanılarak, dize bir veya daha fazla boşluk, sekme ve satırdan oluşan gruplara bölünür. Sonuçta oluşan alt dizelerin bir listesini alırız. Diğer uygulamalarda, daha somut sınırlayıcılar verileri ayırabilir. Bu örnek, bazı veri tabanı araçları tarafından döndürülen verilerde ortak bir ayrıcı olan dizeyi virgüllere ayırır ve böylece ayrıştırır.

```python
>>> satir = 'bob,hacker,41'
>>> satir.split(',')
['bob', 'hacker', '41']
```

Sınırlayıcılar tek bir karakterden daha uzun olabilir.

```python
>>> satir = 'BobSPAMbirSPAMyazılımcıdır'
>>> satir.split('SPAM')
['Bob', 'bir', 'yazılımcıdır']
```

Dilimleme ve bölmenin ayrıştırma potansiyeli sınırlamaları olmasına rağmen, her ikisi de çok hızlı çalışır ve temel metin çıkarma işlerini gerçekleştirebilir. Virgülle ayrılmış metin verileri, _**CSV**_ dosya biçiminin bir parçasıdır; bu konudaki daha gelişmiş araçlar için Python'un standart kütüphanesindeki _**csv**_ modülüne bakın.

### Diğer Yaygın Dize Yöntemleri

Diğer dize yöntemlerinin daha odaklanmış rolleri vardır; örneğin, bir metin satırının sonundaki boşlukları ayırmak, büyük / küçük harf dönüşümleri gerçekleştirmek, içeriği test etmek ve sonunda veya ön kısmında bir alt dizeyi test etmek için:

```python
>>> satir = 'Guido van Rossum\n'
>>> satir.rstrip()
'Guido van Rossum'
>>> satir.upper()
'GUIDO VAN ROSSUM\n'
>>> satir.isalpha()
False
>>> satir.endswith('Rossum\n')
True
>>> satir.endswith('Guido\n')
False
>>> satir.startswith('Guido')
True
```

Dizgi yöntemleriyle aynı sonuçları elde etmek için bazen alternatif teknikler de kullanılabilir; üyelik operatörü \(_**in**_\) bir alt dizenin varlığını test etmek için kullanılabilir ve uzunluk, dilimler işlemleri uçları taklit etmek için kullanılabilir.

```python
>>> satir
'Guido van Rossum\n'
>>> satir.find('Ro') != -1
True
>>> 'Ro' in satir
True
>>> son = 'Rossum\n'
>>> satir.endswith(son)
True
>>> satir[-len(son):] == son
True
>>>
```

Ayrıca, bu bölümün ilerleyen kısımlarında açıklanan biçim dizesi biçimlendirme yöntemlerine bakın; birçok işlemi tek bir adımda birleştiren daha gelişmiş değişiklik araçları sağlar.

Yine, dizeler için çok fazla yöntem olduğundan, burada her birine bakmayacağız. Bu kitabın ilerleyen kısımlarında bazı ek dize örnekleri göreceksiniz, ancak daha fazla ayrıntı için Python kütüphanesi el kitabına ve diğer belge kaynaklarına da başvurabilir veya kendi başınıza etkileşimli olarak deney yapabilirsiniz. Daha fazla ipucu için herhangi bir dize nesnesinin _**help\(s.method\)**_ yöntemi ile sonuçlarını da kontrol edebilirsiniz; Bölüm 4'te gördüğümüz gibi, _**str.method**_ üzerinde _**help**_ çalıştırmak muhtemelen aynı ayrıntıları verir.

Dize yöntemlerinin hiçbirinin desen kabul etmediğini unutmayın; desen tabanlı metin işleme için, bölüm 4'te belirtilen ancak çoğunlukla bu metnin kapsamı dışında olan gelişmiş bir araç olan Python _**re**_ standart kütüphane modülünü kullanmanız gerekir. Bu sınırlama nedeniyle, dize yöntemleri bazen _**re**_ modülünün araçlarından daha hızlı çalışabilir.

### Orijinal Dize Modülünün İşlevleri

Python'un dize yöntemlerinin geçmiş biraz kıvrımlıdır. Varlığının kabaca ilk on yılında, Python, geçerli dize nesnesi yöntemleri kümesini büyük ölçüde yansıtan işlevler içeren _**string**_ adı verilen standart bir kütüphane modülü sağlamıştı. Yoğun talep üzerine, Python 2.0 sürümünde bu fonksiyonlar dize nesnelerinin yöntemi olarak kullanılabilir hale getirildi. Ancak pek çok kişi orijinal dize modülüne dayanan çok fazla kod yazdığı için, geriye dönük uyumluluk için korundu.

Günümüzde, orijinal dize modülünü değil, yalnızca dize yöntemlerini kullanmalısınız. Aslında, bugünün dize yöntemlerinin orijinal modül çağrı formları Python 3.X sürümlerinden itibaren tamamen kaldırılmıştır ve bunları 2.X veya 3.X sürümlerinde yeni bir kod geliştirmesinde kullanmamalısınız. Ancak, hala eski Python 2.X kodunda kullanılan modülü görebildiğiniz için burada kısaca açıklayacağım.

Bu mirasın sonucu, Python 2.X sürümlerinde, teknik olarak gelişmiş dize işlemlerini çağırmanın iki yolu vardır; nesne yöntemlerini çağırarak veya dize modülü işlevlerini çağırarak ve nesneleri bağımsız değişken olarak göndererek. Örneğin, bir dize nesnesine atanan ve bir nesne yöntemini çağıran x değişkeni verildiğinde;

_**x.metod\(argüman\)**_

genellikle aynı işlemi dize modülü aracılığıyla çağırmaya eşdeğerdir. Modülü daha önceden içe aktarmış olmanız koşuluyla;

_**string.metod\(x, argüman\)**_

Yöntemin kullanılmasına bir örnek;

```python
>>> s = 'a+b+c'
>>> x = s.replace('+', 'AA')
>>> x
'aAAbAAc'
```

Aynı işleme Python 2.X sürümlerindeki dize modülü aracılığıyla erişmek için, modülü içe aktarmanız ve nesneyi iletmeniz gerekir.

```python
# Python 2.X sürümleri için;
>>> import string
>>> s = 'a+b+c'
>>> x = string.replace(s, '+', 'AA')
>>> x
'aAAbAAc'
```

Modül yaklaşımı çok uzun süredir standart olduğundan ve dizeler çoğu programın bu kadar merkezi bir bileşeni olduğundan, karşılaştığınız Python 2.X kodunda her iki arama düzenini de görebilirsiniz.

Yine de, bugün her zaman eski modül çağrıları yerine yöntem çağrılarını kullanmalısınız. Bunun için modül çağrılarının 3.X sürümlerinde kaybolmasının yanı sıra iyi sebepleri de vardır. Birincisi; modül çağrı şeması dize modülünü içe aktarmanızı \(_**import**_\) gerektirir. Bir diğeri ise; modül yazmak için birkaç karakter daha uzu narama yapar. Son olarak, modül yöntemlerden daha yavaş çalışır.

Orijinal dize modülünün kendisi, dize yöntemi eşdeğerleri olmadan Python 3.X sürümleri de korunur, çünkü önceden tanımlanmış dize sabitleri \(ör. _**string.digits**_\) ve bir _**Template**_ nesne sistemi, dize biçimi yöntemidir ve burada büyük ölçüde atlanmıştır. 2.X kodunuzu 3.X sürümlerinde kullanmak için gerçekten değiştirmek istemediğiniz sürece, içindeki herhangi bir temel dize işlemi çağrısının sadece Python geçmişinin hayaletleri olduğunu düşünmelisiniz.

## Dize Biçimlendirme İfadeleri

Bu bölümde tanıştığımız dize yöntemleri ve dizi işlemleri ile birçok şey yapabilirsiniz, ancak Python da dize işleme görevlerini birleştirmek için daha gelişmiş bir yol sağlamaktadır. Gerekli olmamakla birlikte; özellikle bir programın kullanıcılarına görüntülenecek metni biçimlendirirken uygun olabilir. Python dünyasındaki yeni fikirlerin zenginliği nedeniyle, dize biçimlendirme bugün Python'da iki çeşide sahiptir.

#### _**Dize biçimlendirme ifadeleri: '...%s...' % \(değer\)**_

Python'un kuruluşundan bu yana mevcut olan orijinal teknik; bu form C dilinin _**"printf"**_ modeline dayanmaktadır ve mevcut kodların çoğunda yaygın olarak kullanılmaktadır.

### _**Dize biçimlendirme yöntemi çağrısı: '...{}...'.format\(değer\)**_

Python 2.6 ve 3.0 sürümlerinde daha yeni bir teknik eklenmiştir ve bu form kısmen C\# / .NET'te aynı adlı bir araçtan türetilir ve dize biçimlendirme ifadesi işlevselliği ile çakışmaktadır.

Yöntem çağrısı daha yeni olduğu için, bunlardan birinin veya diğerinin zamanla kullanımdan kaldırılması ihtimali vardır. Python 3.0 sürümü 2008'de kullanıma sunulduğunda, ifadenin sonraki Python sürümlerinde kullanımdan kaldırılması daha yüksek olasılık olarak görünüyordu. Ancak günümüzde bu ifadenin geniş kullanımı göz önüne alındığında bu pek olası görünmüyor. Aslında; bugün Python'un kendi standart kütüphanesinde bile binlerce kez bu şekilde kullanılıyor.

Doğal olarak, bu hikayenin gelişimi Python kullanıcılarının gelecekteki uygulamalarına bağlıdır. Öte yandan, hem ifade hem de yöntem bugün kullanım için geçerli olduğundan ve karşılaşacağınız kodda görünebileceğinden, bu kitap her iki tekniği de burada tam olarak ele almaktadır. Gördüğünüz gibi, ikisi bir temanın büyük ölçüde varyasyonlarıdır, ancak yöntemin bazı ekstra özellikleri vardır ve ifade genellikle daha özlüdür, çoğu Python programcısı için çok doğal gelmektedir.

Bu kitabın kendisi, açıklama amacıyla daha sonraki örneklerde her iki tekniği de kullanmaktadır. Bununla birlikte; eğer Python etkileşimli oturumda aşağıdaki komutu çalıştırırsanız Python'un mottosunda aşağıdaki iletiyi görebilirsiniz.

```python
import this
```

{% hint style="info" %}
Bunu yapmanın tek ve tercihen tek bir yolu olmalı.
{% endhint %}

Daha yeni dize biçimlendirme yöntemi orijinal ve yaygın olarak kullanılan ifadeden daha iyi değilse, bu alandaki Python programcılarının bilgi tabanı gereksinimlerinin iki katına çıkarılması, o terimin orijinal ve uzun süreli anlamı gereği gereksiz ve hatta _**Pythonic**_ gibi görünür. Bu araçlar büyük ölçüde çakışıyorsa, programcılar iki karmaşık araç öğrenmek zorunda kalmamalıdır. Biçimlendirmenin eklenen dil yığını hak ekip etmediğini kendiniz değerlendirmelisiniz, bu yüzden her ikisine de adil bir şekilde değinelim.

### Biçimlendirme İfadelerinin Temelleri

Dize biçimlendirme ifadeleri bu bölümde orijinal olduğundan, onlarla başlayalım. Python, dizeler üzerinde çalışmak için _**%**_ ikili operatörünü tanımlar. Bu operatörün aynı zamanda sayılar için bölme veya modül operatörü olduğunu da hatırlayabilirsiniz. Dizelere uygulandığında, _**%**_ operatörü bir biçim tanımına göre değerleri dize olarak biçimlendirmek için basit bir yol sağlar. Kısacası, _**%**_ operatörü, parçaları ayrı ayrı oluşturmak ve birleştirmek yerine, birden fazla dize ikamesini tek seferde kodlamak için kompakt bir yol sağlar.

**Dizeler biçimlendirmek için;**

1. _**%**_ işlecinin sol tarafında, her biri _**%**_ ile başlayan \(veya %d\) bir veya daha fazla eklenmiş dönüşüm hedefi içeren bir biçim dizesi sağlanır.
2. _**%**_ işlecinin sağında; Python'un dönüşüm hedefi \(veya hedefleri\) yerine soldaki biçim dizesine eklenmesini istediğiniz nesne \(veya bir tuple için yerleştirilmiş nesneleri\) sağlanır.

Örneğin, aşağıda gördüğümüz biçimlendirme örneğinde; 3 tam sayı soldaki biçim dizesindeki _**%d**_ yerine, _**'Python'**_ ifadesi ise _**%s**_ yerine geçer. Sonuçta, diğer rollerde kullanılmak üzere yazdırılabilen veya kaydedilebilen bu iki ikameyi yansıtan yeni bir dizedir.

```python
>>> 'Bu örnekte kullandığımız %s örneği %d sürümündedir.' % ('Python', 3)
'Bu örnekte kullandığımız Python örneği 3 sürümündedir.'
```

Teknik olarak konuşursak, dize biçimlendirme ifadeleri genellikle isteğe bağlıdır; genellikle birden çok birleşim ve dönüşümle benzer işler yapabilirsiniz. Ancak biçimlendirme, birçok adımı tek bir işlemde birleştirmenize olanak tanır.

```python
>>> metin = 'Python'
>>> '%s Guido van Rossum tarafından temelleri atılan bir yazılım dilidir.' % metin      # Dize birleştirme
'Python Guido van Rossum tarafından temelleri atılan bir yazılım dilidir.'
>>> '%d %s %g' % (1, 'Python', 3.0)     # Türe özgü değiştirme
'1 Python 3'
>>> '%s -- %s -- %s' % (42, 3.14, [3, 2, 1])    # Tüm türler %s hedefi ile kullanılabilir
'42 -- 3.14 -- [3, 2, 1]'
>>>
```

Buradaki ilk örnekte _**%s**_ işaretinin yerine _**'Python'**_ dizesini soldaki hedef ile değiştirir. İkinci örnekte, hedef dizeye üç değer eklenir. Birden fazla değer eklerken, sağdaki değerleri parantez içinde gruplamanız \(yani bir tuple nesnesinin içine eklemeniz\) gerektiğini unutmayın. _**%**_ biçimlendirme ifadesi işlecinin; sağ tarafında tek bir öge ya da bir veya daha fazla ögeden oluşan bir grup beklemektedir.

Üçüncü örnek yine üç değer ekler; bir tam sayı, bir kayan nokta nesnesi ve bir liste nesnesi; ancak soldaki tüm hedeflerin dizeye dönüşüm anlamına gelen _**%s**_ ifadesi olduğunu unutmayın. Her nesne türü bir dizeye \(yazdırma sırasında kullanılan nesne\) dönüştürülebileceğinden, her nesne türü _**%s**_ dönüşüm koduyla çalışır. Bu nedenle, bazı özel biçimlenme işlemi yapmayacaksanız; _**%s**_ genellikle biçimlendirme ifadesi için hatırlamanız gereken tek koddur.

{% hint style="info" %}
Biçimlendirmenin soldaki dizeyi değiştirmek yerine her zaman yeni bir dize oluşturduğunu unutmayın. Dizeler değişmez \(_**immutable**_\) olduğundan, bu şekilde çalışmaktadır. Daha önce olduğu gibi, eğer sonucu kullanmak istiyorsanız, bir değişken adına atayın.
{% endhint %}

### Gelişmiş Biçimlendirme İfadesi Söz Dizimi

Daha gelişmiş; türe özgü biçimlendirme için, Tablo 7-4'te listelenen dönüşüm türü kodlarından herhangi birini ifadelerin biçimlendirilmesinde kullanabilirsiniz; ikame hedeflerindeki _**%**_ karakterinden sonra eklenmelidir. C programcıları bunların çoğunu tanıyacaktır, çünkü Python dize biçimlendirmesi olağan C _**printf**_ biçim kodlarının tümünü destekler. Ancak _**printf**_ gibi göstermek yerine sonucu döndürür. Tablodaki biçim kodlarından bazıları, aynı türü biçimlendirmek için alternatif yollar sağlar. Örneğin; _**%e**_, _**%f**_ ve _**%g**_ kayan noktalı sayıları biçimlendirmek için alternatif yollar sağlamaktadır.

Tablo 7-4. Dize Biçimlendirme Tür Kodları

| Kod | Yorumlama |
| :--- | :--- |
| s | Dize veya str\(değer\) |
| r | s ile aynıdır ancak str yerine repr kullanır |
| c | Karakter \(int veya str\) |
| d | Ondalık \(10 tabanında tam sayı\) |
| i | Tam sayı |
| u | d ile aynıdır ancak pozitif, negatif işaretini göstermez |
| o | Sekizlik \(8 tabanında tam sayı\) |
| x | Onaltılık \(16 tabanında tam sayı\) |
| X | x ile aynıdır ancak büyük harflerle |
| e | Üslü kayan noktalı sayı, küçük harf |
| E | e ile aynıdır, ancak büyük harflerle |
| f | Kayan noktalı ondalık sayı |
| F | f ile aynıdır, ancak büyük harflerle |
| g | e veya f'nin kayan noktası |
| G | E veya F'nin kayan noktası |
| % | Değişmez % \(%% olarak kullanılır\) |

Aslında, ifadenin sol tarafındaki biçim dizesindeki dönüşüm hedefleri, tamamen kendi başına oldukça karmaşık bir söz dizimiyle çeşitli dönüştürme işlemlerini destekler. Dönüşüm hedeflerinin genel yapısı aşağıdaki şekilde görünür.

```python
%[(anahtar_adı)][bayrak][genişlik][.hassasiyet]tür_kodu
```

Tablo 7-4'ün ilk sütunundaki tür kodu karakterleri, bu hedef dizenin biçiminin sonunda görünür. _**%**_ ve tür kodu karakteri arasında aşağıdakilerden herhangi birini yapabilirsiniz.

* İfadenin sağ tarafında kullanılan sözlüğün \(_**dict**_\) dizine eklemek için bir anahtar adı girilebilir.
* Sola yaslama, pozitif veya negatif sayılardan önce boşluk veya sıfır doldurma gibi şeyleri belirten bayrakları
* Değiştirilen metin için toplam minimum alan genişliği verebilirsiniz
* Kayan nokta sayıları için ondalık noktadan sonra görüntülenecek basamak sayısını \(hassasiyetini\) ayarlayabilirsiniz

Hem genişlik \(_**width**_\) hem de hassasiyet \(_**precision**_\) değerlerini ifadenin sağ tarafındaki giriş değerlerindeki bir sonraki ögeden almaları gerektiğini belirtmek için olarak kodlanabilir. Bu, çalışma zamanına kadar bilinmiyorsa yararlıdır. Buna ek araçlardan herhangi birine ihtiyacınız yoksa, biçim dizesindeki basit bir _**%s**_ türünden bağımsız olarak karşılık gelen değerin varsayılan yazdırma dizesiyle değiştirilir.

### Gelişmiş Biçimlendirme İfadesi Örnekleri

Hedef söz diziminin biçimlendirilmesi, Python standart kılavuzlarında ve referans metinlerinde tam olarak belgelenmiştir, ancak ortak kullanımı göstermek için birkaç örneğe bakalım. Bu, varsayılan olarak tam sayıları ve ardından sola yaslama ve sıfır dolgusu ile altı karakterlik bir alanda biçimlendirir.

```python
>>> x = 1234
>>> sonuc = 'Sayilar: ...%d ...%-6d...%06d' % (x, x, x)
>>> sonuc
'Sayilar: ...1234 ...1234  ...001234'
```

_**%e**_, _**%f**_ ve _**%g**_ biçimleri, aşağıdaki etkileşimin gösterdiği gibi kayan nokta sayılarını farklı şekillerde görüntüler. _**%E**_ ve _**%e**_ aynıdır ancak üst büyük harftir ve _**%g**_ biçimleri sayının içeriğine göre seçer. Resmi olarak; sayı 4'ten küçük veya hassasiyetten küçük değilse üstel biçim _**%e**_'yi kullanmak için tanımlanır; aksi takdirde varsayılan toplam basamak hassasiyeti 6 olan ondalık biçim _**%f**_'yi kullanır.

```python
>>> x = 1234566789
>>> x
1234566789
>>> '%e | %f | %g' % (x, x, x)  # Python 3.10 sürümü
'1.234567e+09 | 1234566789.000000 | 1.23457e+09'
>>> '%E' % x  # Python 3.10 sürümü
'1.234567E+09'
>>>
```

Kayan nokta sayıları için; sola yaslama, sıfır dolgu, sayısal işaretler, toplam alan genişliği ve ondalık noktadan sonraki rakamları belirterek çeşitli ek biçimlendirme efektleri elde edebilirsiniz. Daha basit görevler için _**%s**_ biçim ifadesine sahip dizelere veya daha önce gösterilen _**str**_ yerleşik işlevine dönüştürerek başlayabilirsiniz.

```python
>>> '%6.2f | %05.2f | %+06.1f' % (x, x, x)
'1234566789.00 | 1234566789.00 | +1234566789.0'
>>> '%s' % x, str(x)
('1234566789', '1234566789')
```

Boyutlar çalışma zamanına kadar bilinmiyorsa, değerlerini _**%**_ operatörünün sağındaki girdilerdeki bir sonraki öğeden alınmaya zorlamak için biçim dizesinde _**\***_ işaretleyerek hesaplanmış bir genişlik ve hassasiyet kullanabilirsiniz.

```python
>>> '%f, %.2f, %.*f' % (1/3.0, 1/3.0, 4, 1/3.0)
'0.333333, 0.33, 0.3333'
```

Bu özellik ile ilgileniyorsanız, daha fazla bilgi için bu örneklerden ve işlemlerden bazılarını kendi başınıza etkileşimli bilgi isteminde deneyebilirsiniz.

### Sözlük Tabanlı Biçimlendirme İfadeleri

Daha gelişmiş bir uzantı olarak; dize biçimlendirme, soldaki dönüşüm hedeflerinin sağda kodlanmış bir sözlükteki \(_**dict**_\) anahtarlara başvurmasına ve karşılık gelen değerleri almasına da izin verir. Bu, biçimlendirmeyi bir tür şablon aracı olarak kullanma kapısını açar. Şu ana kadar bölüm 4'te kısa bir süre için sözlükler ile çalışmıştık; ancak temellerini gösteren bir örnek yapalım.

```python
>>> 'Kullanılan %(dil)s sürümü %(surum).1f' % {'surum': 3.10, 'dil': 'Python'}
'Kullanılan Python sürümü 3.10'
```

Burada, soldaki biçim dizesindeki \(dil\) ve \(surum\), sağdaki sözlük değişmezindeki anahtarları belirtir ve ilişkili değerlerini getirir. _**HTML**_ veya _**XML**_ gibi metin üreten programlar genellikle bu tekniği kullanır; bir değer sözlüğü oluşturabilir ve bunları anahtar tabanlı referanslar kullanan tek bir biçimlendirme ifadesiyle aynı anda değiştirebilirsiniz.

```python
>>> ileti = """
... Merhaba;
... Günaydın %(isim)s
... Yaşınız %(yas)s
... """
>>> deger = {'isim': 'Guido van Rossum', 'yas': 64}
>>> print(ileti % deger)

Merhaba;
Günaydın Guido van Rossum
Yaşınız 64
```

Bu hile ayrıca; çağırıldığı yerde var olan tüm değişkenleri içeren bir sözlük döndüren _**vars**_ yerleşik işleviyle birlikte kullanılır.

```python
>>> isim = 'Guido van Rossum'
>>> yas = 64
>>> vars()
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, 'ileti': '\nMerhaba;\nGünaydın %(isim)s\nYaşınız %(yas)s\n', 'deger': {'isim': 'Guido van Rossum', 'yas': 64}, 'isim': 'Guido van Rossum', 'yas': 64}
>>>
```

{% hint style="info" %}
_**vars**_ yerleşik işlevini çağırdığımız zaman geri dönen içerikte diğer yerleşik işlevlerinde döneceğini unutmayın.
{% endhint %}

Biçimlendirme işleminin sağ tarafında kullanıldığında, biçim dizesinin değişkenlere ada göre sözlük anahtarları olarak başvurmasını sağlar.

```python
>>> surum = 3.10
>>> dil = 'Python'
>>> 'Kullanılan %(dil)s sürümü %(surum).1f' % vars()
'Kullanılan Python sürümü 3.10'
```

Sözlükleri bölüm 8'de daha derinlemesine inceleyeceğiz. Burada tekrarlamayacağımız _**%x**_ ve _**%o**_ biçimlendirme ifadesi hedef kodlarıyla onaltılık ve sekizlik sayı dizelerine dönüşen örnekler için bölüm 5'e bakın. Ek biçimlendirme ifadesi örnekleri de biçimlendirme yöntemiyle karşılaştırmalar olarak görünür.

## Dize Biçimlendirme Yöntemi Çağrıları

Daha önce de belirtildiği gibi, Python 2.6 ve 3.0, bazıları tarafından biraz daha Python'a özgü olarak görülen dizeleri biçimlendirmek için yeni bir yol tanıtmıştır. İfadeleri biçimlendirmenin aksine, biçimlendirme yöntemi çağrıları C dilinin _**"printf"**_ modeline çok yakın değildir ve bazen daha açıktır. Öte yandan, yeni teknik hala tür kodları ve biçimlendirme özellikleri gibi temel _**"printf"**_ konseptlerine dayanmaktadır. Dahası, ifadeleri biçimlendirmekle büyük ölçüde örtüşüyor, bazen biraz daha fazla kod gerektiriyor ve pratikte birçok rolde karmaşık olabiliyor. Bu nedenle, bugün ifadeler ve yöntem çağrıları arasında en iyi kullanım önerisi bulunmamaktadır. Çoğu programcı her iki şemaya da iyi bir anlayışla kullanmaktadır. Neyse ki, ikisi birçok temel kavramın örtüşmesine yetecek kadar benzemektedir.

### Biçimlendirme Yönteminin Temelleri

Dize nesnesinin biçim yöntemi, Python 2.6, 2.7 ve 3.X sürümlerinde kullanılabilir, bir ifade yerine normal işlev çağrısı söz dizimini temel alır. Özellikle, konu dizesini şablon olarak kullanır ve şablona göre değiştirilecek değerleri temsil eden herhangi bir sayıda argümanı alır.

Kullanımı işlevler ve çağrılar hakkında bilgi gerektirir; ancak çoğunlukla bu basittir. Konu dizesi içinde, süslü parantezler, konum \(örn. {1}, {2}\) veya anahtar kelime \(örn. {dil}, {surum}\) veya 2.7 ve 3.1 sürümlerinden itibaren \({}\) göreli konum olarak eklenecek ikame hedeflerini ve bağımsız değişkenleri belirtir. Bölüm 18'de derinlemesine iletilen argümanları incelediğimizde göreceğimiz gibi, işlevlere ve yöntemlere yönelik argümanlar konum veya anahtar kelime adıyla iletilebir ve Python'un keyfi olarak birçok konumsal ve anahtar kelime argümanı toplama yeteneği bu genel yöntem çağrısı kalıplarına izin verir.

```python
>>> sablon = '{0} {1} ve {2}'   # Konumsal
>>> sablon.format('Guido', 'van Rossum', 'Python')
'Guido van Rossum ve Python'
>>> sablon = '{adi} {soyadi} ve {dil}'  # Anahtar Kelime
>>> sablon.format(adi='Guido', soyadi='van Rossum', dil='Python')
'Guido van Rossum ve Python'
>>> sablon = '{adi} {0} ve {dil}' # Konumsal ve Anahtar Kelime
>>> sablon.format('van Rossum', dil='Python', adi='Guido')
'Guido van Rossum ve Python'
>>> sablon = '{} {} ve {}'  # Konum sırasına göre
>>> sablon.format('Guido', 'van Rossum', 'Python')
'Guido van Rossum ve Python'
```

Karşılaştırıldığında, son bölümün biçimlendirme ifadesi biraz daha özlü olabilir, ancak anahtar kelime argümanları yerine sözlükler kullanır ve değer kaynakları için çok fazla esnekliğe izin vermez.

```python
>>> sablon = '%s %s ve %s'
>>> sablon % ('Guido', 'van Rossum', 'Python')
'Guido van Rossum ve Python'
>>> sablon = '%(adi)s %(soyadi)s ve %(dil)s'
>>> sablon % dict(dil='Python', adi='Guido', soyadi='van Rossum')
'Guido van Rossum ve Python'
```

Bölüm 4'te tanıtılan ve bölüm 8'de detaylı olarak incelenecek olan anahtar kelime argümanlarından sözlük yapmak için _**dict\(\)**_ işlevinin kullanıldığına dikkat edin; {...} değişmezine genellikle daha az karmaşık bir alternatiftir. Doğal olarak, biçim yöntemi çağrısındaki konu dizesi, geçici bir dize oluşturan bir değişmez de olabilir ve isteğe bağlı nesne türleri; ifadenin _**%s**_ kodu gibi hedeflerle değiştirilebilir.

```python
>>> '{isim}, {0} ve {liste}'.format(3.14, isim='Guido van Rossum', liste=[1,2,3])
'Guido van Rossum, 3.14 ve [1, 2, 3]'
```

_**%**_ ifadesi ve diğer dize yöntemlerine olduğu gibi; format hemen yazdırılabilen veya daha fazla çalışma için kaydedilebilen yeni bir dize nesnesi oluşturur ve döndürür. _**Dizelerin değişmez olduğunu hatırlayın, bu nedenle biçim gerçekten yeni bir nesne oluşturmalıdır.**_ Dize biçimlendirme yalnızca görüntüleme amaçlı değildir.

```python
>>> x = '{isim}, {0} ve {liste}'.format(3.14, isim='Guido van Rossum', liste=[1,2,3])
>>> x
'Guido van Rossum, 3.14 ve [1, 2, 3]'
>>> x.split('ve')
['Guido van Rossum, 3.14 ', ' [1, 2, 3]']
>>> y = x.replace('ve', 'veya')
>>> y
'Guido van Rossum, 3.14 veya [1, 2, 3]'
```

### Anahtar, Nitelik ve İndeks Ekleme

_**%**_ biçimlendirme ifadeleri gibi, biçim çağrıları da daha gelişmiş kullanımı desteklemek için daha karmaşık hale gelebilir. Örneğin, biçim dizeleri nesne özniteliklerini ve sözlük anahtarlarını adlandırabilir. Normal Python söz diziminde olduğu gibi köşeli parantezler sözlük anahtarları ve noktalar, konum veya anahtar sözcük tarafından başvurulan bir öğenin nesne anahtarındaki bir sözlüğü dizine ekler ve daha sonra içe aktarılan _**sys**_ modülü nesnesinden _**platform**_ özelliğini alır. İkincisi de aynısını yapar, ancak nesneleri konum yerine anahtar kelimeye göre adlandırır.

```python
>>> import sys
>>> 'Kullandığım {1[cihaz]} {0.platform} platformunda çalışmaktadır.'.format(sys, {'cihaz': 'laptop'})
'Kullandığım laptop win32 platformunda çalışmaktadır.'
>>> 'Kullandığım {map[cihaz]} {sys.platform} platformunda çalışmaktadır.'.format(sys=sys, map={'cihaz': 'laptop'})
'Kullandığım laptop win32 platformunda çalışmaktadır.'
>>>
```

Biçim dizelerindeki köşeli parantezler, dizinleme yapmak için liste ötelemelerini de adlandırabilir, ancak biçim dizeleri içinde yalnızca tek pozitif ötelemeler söz dizimsel olarak çalışır. Bu nedenle bu özellik düşündüğünüz kadar genel değildir. _**%**_ ifadelerinde olduğu gibi, negatif indeksleri veya dilimleri adlandırmak veya genel olarak rastgele ifade sonuçlarını kullanmak için, ifadeleri biçim dizesinin dışında çalıştırmanız gerekir.

```python
>>> liste = list('Python')
>>> liste
['P', 'y', 't', 'h', 'o', 'n']
>>> 'birinci={0[0]}, üçüncü={0[2]}'.format(liste)
'birinci=P, üçüncü=t'
>>> 'birinci={0}, sonuncu={1}'.format(liste[0], liste[-1])
'birinci=P, sonuncu=n'
>>> bolumler = liste[0], liste[-1], liste[1:5]
>>> 'birinci={0}, sonuncu={1}, arası={2}'.format(*bolumler)
"birinci=P, sonuncu=n, arası=['y', 't', 'h', 'o']"
>>>
```

### Gelişmiş Biçimlendirme Yöntemi Söz Dizimi

_**%**_ ifadeleriyle bir başka benzerlik; biçim dizesine fazla söz dizimi ekleyerek daha spesifik dizilimler elde edebilmenizdir. Biçimlendirme yöntemi için, muhtemelen boş ikame hedefinin tanımlanmasından sonra iki nokta üst üste işaretini ve ardından alan boyutunu, gerekçeyi ve belirli bir tür kodunu adlandırabilen bir biçim belirteci kullanırız. Biçim dizesinde ikame hedefi olarak görünebilecek öğelerin biçimsel yapısı; dört bölümünün tümü isteğe bağlıdır ve araya girmeden boşluk bırakılması gerekir.

```python
{alanadı bileşen !dönüşüm bayrağı :format özellikleri}
```

Bu ikame hedefi söz diziminde;

* _**alanadı**_; 2.7, 3.1 ve sonraki Python sürümlerinde göreceli bağımsız değişken numaralandırmasıyla kullanılmayabilecek, bağımsız değişkeni tanımlayan isteğe bağlı bir sayı veya anahtar kelimedir.
* _**bileşen**_; bağımsız değişkenin tüm özelliklerini kullanmak için atlanabilecek, bağımsız değişkenin özniteliklerini ve dizinlenmiş değerlerini çağırmak için kullanılan sıfır veya daha fazla _**".isim"**_ veya _**"\[indeks\]"**_ referans dizesidir.
* _**dönüşüm bayrağı**_; ! ile başlar ve eğer varsa _**r**_, _**s**_ veya _**a**_ ile başlar ve sırasıyla _**repr**_, _**str**_ veya _**ascii**_ yerleşik işlevlerini çağırmak için kullanılır.
* _**format özellikleri**_; alan genişliği, hizalama, doldurma, ondalık hassasiyeti gibi ayrıntılar da dahil olmak üzere değerin nasıl gösterilmesi gerektiğini belirten bir metinle başlar ve isteğe bağlı bir veri türü koduyla biter.

İki nokta üst üste karakterinden sonraki _biçim özellikleri_ \(_**formatspec**_\) bileşeni tamamen kendi başına zengin bir biçime sahiptir ve resmi olarak aşağıdaki şekilde açıklanır.

Parantezler isteğe bağlı bileşenleri belirtir ve tam anlamıyla kodlanmaz.

```text
[[dolgu]hizalama][işaret][#][0][genişlik][,][.hassasiyet][tür kodu]
```

Burada doldu, "{" veya "}" dışında herhangi bir dolgu karakteri olabilir; hizalama sırasıyla, sol hizalama, sağ hizalama, bir eşittir karakterinden sonra dolgu veya ortalanmış hizalama için sırasıyla &lt;, &gt;, = veya ^ olabilir. +, - veya boşluk olabilir; ve virgül seçeneği Python 2.7 ve 3.1 sürümlerinden itibaren binlerce ayırıcı için virgül kullanır. Genişlik ve hassasiyet _**%**_ ifadesine olduğu gibi _biçim özellikleri_ değişkenleri dinamik olarak almak için yalnızca alan adlarına sahip iç içe {} biçim dizeleri içerebilir.

Yöntemin tür kodu seçenekleri, _**%**_ ifadelerinde kullanılan ve daha önce tablo 7-4'te listelenenlerle neredeyse tamamen eşleşir, ancak biçim yöntemi; tam sayıları ikili biçimde görüntülemek için _**b**_ tür kodlara \(_**bin**_ yerleşik çağrısını kullanmaya eşdeğer\) da izin verir; _**%**_ tür kodunun yüzdeleri görüntülemesine izin verir; yalnızca 10 tabanında tam sayılar için _**d**_ kullanır. Burada _**i**_ ve _**u**_ kullanılmaz. İfadenin _**%s**_ öğesinin aksinin, buradaki _**s**_ türü kodunun bir dize nesnesi bağımsız değişkeni gerektirdiğini unutmayın; herhangi bir türü genel olarak kabul etmek için tür kodunu atlayın.

Burada atladığımız ikame söz dizimi hakkında daha fazla bilgi için Python'un kütüphane kılavuzuna bakın. Dizenin biçim yöntemine ek olarak, tek bir nesne biçim _**"format\(nesne, biçim özelliği\)"**_ yerleşik işleviyle de biçimlendirilebilir. _**"format"**_ işlev aşırı yüklemesi ile kullanıcı tanımlı sınıflarda özelleştirilebilir bir yöntemdir.

### Gelişmiş Biçimlendirme Yöntemi Örnekleri

Anlayacağınız gibi, söz dizimi biçimlendirme yöntemlerinde karmaşık olabilir. Bu gibi durumlarda en iyi dostunuz genellikle burada etkileşimli bilgi istemi olduğundan, bazı örneklere bakalım. Aşağıda, **{0:10}**, 10 karakter genişliğinde bir alandaki ilk konum bağımsız değişkeni, **{1:&lt;10}**, 10 karakter genişliğinde bir alanda sola yaslanmış ikinci konum bağımsız değişkeni ve **{0.platform&gt;10}**, 10 karakter genişliğinde bir alanda düzeltilmiş ilk argüman _**platform**_ niteliği anlamına gelir.

```python
>>> '{0:10} = {1:10}'.format('Monty', 123.4567)
'Monty      =   123.4567'
>>> '{0:>10} = {1:<10}'.format('Monty', 123.4567)
'     Monty = 123.4567  '
>>> '{0.platform:>10} = {1[cihaz]:<10}'.format(sys, dict(cihaz='laptop'))
'     win32 = laptop    '
```

Her durumda, göreceli otomatik numaralandırma ile soldan sağa seçerseniz, Python 2.7 ve 3.1'den sonraki sürümlerde bağımsız değişken numarasını atlayabilirsiniz; ancak bu kodunuzu daha az açık hale getirir; böylece biçimlendirme yönteminin bildirilen avantajlarından birini reddeder.

```python
>>> '{:10} = {:10}'.format('monty', 123.4567)
'monty      =   123.4567'
>>> '{:>10} = {:<10}'.format('monty', 123.4567)
'     monty = 123.4567  '
>>> '{.platform:>10} = {[cihaz]:<10}'.format(sys, dict(cihaz='laptop'))
'     win32 = laptop
```

Kayan noktalı sayılar, biçimlendirme yöntemi çağrılarında _**%**_ ifadelerinde olduğu gibi aynı tür kodlarını ve biçimlendirme özgünlüğünü destekler. Örneğin; aşağıdaki _**"{2: g}"**_; **g** kayan nokta gösterimine göre varsayılan olarak biçimlendirilen üçüncü bağımsız değişkeni ifade eder. _**"{1: .2f}"**_ yalnızca iki ondalık basamaklı **f**; kayan nokta biçimini ve _**"{2: 06.2f}"**_ soluna altı karakter genişliğinde ve sıfır dolgulu alan ekler.

```python
>>> '{0:e}, {1:.3e}, {2:g}'.format(3.14159, 3.14159, 3.14159)
'3.141590e+00, 3.142e+00, 3.14159'
>>> '{0:f}, {1:.2f}, {2:06.2f}'.format(3.14159, 3.14159, 3.14159)
'3.141590, 3.14, 003.14'
```

Onaltılı, sekizli ve ikili biçimler de biçim yöntemi tarafından desteklenir. Aslında, dize biçimlendirme, tam sayıları belirli bir tabana biçimlendiren yerleşik işlevlerin bazılarına alternatiftir.

```python
>>> '{0:X}, {1:o}, {2:b}'.format(255, 255, 255) # Onaltılı, Sekizli, İkili
'FF, 377, 11111111'
>>> bin(255), int('11111111', 2), 0b11111111    # İkiliye dönüştürme
('0b11111111', 255, 255)
>>> hex(255), int('FF', 16), 0xFF       # Onaltılıya dönüştürme
('0xff', 255, 255)
>>> oct(255), int('377', 8), 0o377      # Sekizliye dönüştürme
('0o377', 255, 255)
```

Biçimlendirme parametreleri, biçimlendirme dizelerinde sabit olarak kodlanabilir veya ifadelerin genişliğini ve hassasiyetini biçimlendirmedeki \* söz dizimine benzer şekilde iç içe biçim söz dizimiyle dinamik olarak argümanlar listesinden alınabilir.

```python
>>> '{0:.2f}'.format(1 / 3.0)   # Sabit yazılan parametre
'0.33'
>>> '%.2f' % (1 / 3.0)
'0.33'
>>> '{0:.{1}f}'.format(1 / 3.0, 4)      # Bağımsız değişkenden değer alma
'0.3333'
>>> '%.*f' % (4, 1 / 3.0)
'0.3333'
```

Son olarak; Python 2.6 ve 3.0 sürümlerinden itibaren, tek bir öğeyi biçimlendirmek için kullanılabilen yeni bir yerleşik biçim işlevi de mevcuttur. Dize biçimi yöntemine daha özlü bir alternatiftir ve tek bir öğeyi % biçimlendirme ifadesiyle biçimlendirmeye kabaca benzemektedir.

```python
>>> '{0:.2f}'.format(1.2345)    # Dize metodu
'1.23'
>>> format(1.2345, '.2f')       # Yerleşik işlev
'1.23'
>>> '%.2f' % 1.2345     # İfade
'1.23'
```

Teknik olarak, yerleşik biçim, nesnenin _**str.format**_ yönteminin biçimlendirilmiş her öğe için dahili olarak yaptığı _**format**_ yöntemini çalıştırır. Yine de, burada orijinal _**%**_ ifadesinin eşdeğerinden daha ayrıntılıdır.

### % Biçim İfadesi İle Karşılaştırma

Önceki bölümleri yakından incelerseniz, muhtemelen en az konumsal referanslar ve sözlük anahtarları için; dize biçimi yönteminin özellikle biçimlendirme ifadesi ve ekstra biçimlendirme söz dizimi ile gelişmiş kullanımda _**%**_ biçimlendirme ifadesine çok benzediğini fark edeceksiniz. Aslında, yaygın olarak kullanılan durumda, özellikle genel _**%s**_ yazdırma dizesi değiştirme hedefini kullandığınızda ve hatta 2.7 ve 3.1 sürümlerinde eklenen alanların otomatik numaralandırılmasında bile, biçimlendirme ifadelerini; biçimlendirme yöntem çağrılarından daha kolay kodlayabilirsiniz.

```python
>>> print('%s=%s' % ('monty python', 42))       # Tüm sürümler için biçimlendirme ifadesi
monty python=42
>>> print('{0}={1}'.format('monty python', 42)) # Python 2.6 ve 3.0 sürümlerinden itibaren
monty python=42
>>> print('{}={}'.format('monty python', 42))   # Python 2.7 ve 3.1 sürümlerinden itibaren otomatik numaralandırma
monty python=42
>>>
```

Bu aşamada göreceğimiz gibi; daha karmaşık biçimlendirme, karmaşıklık açısından artmaktadır ve bazıları, ifadenin yaygınlığı göz önüne alındığında biçimlendirme yöntemini gereksiz olarak görmektedir.

Öte yandan, biçimlendirme yöntemi de birkaç potansiyel avantaj sunmaktadır. Örneğin; orijinal _**%**_ ifadesi anahtar kelimeleri, öznitelik başvurularını ve ikili tür kodlarını işleyemez, ancak _**%**_ biçim dizelerindeki sözlük anahtar başvurularında genellikle benzer hedeflere ulaşabilir. İki tekniğin nasıl kesiştiğini görmek için; aşağıdaki _**%**_ ifadelerini daha önce gösterilen eşdeğer biçim yöntemi çağrılarıyla karşılaştıralım.

```python
>>> '%s, %s and %s' % (3.14, 42, [1, 2])
'3.14, 42 and [1, 2]'
>>> 'Kullandığım %(cihaz)s %(platform)s platformunda çalışmaktadır.' % {'cihaz': 'laptop', 'platform': sys.platform}
'Kullandığım laptop win32 platformunda çalışmaktadır.'
>>> 'Kullandığım %(cihaz)s %(platform)s platformunda çalışmaktadır.' % dict(cihaz='laptop', platform=sys.platform)
'Kullandığım laptop win32 platformunda çalışmaktadır.'
>>> liste = list('Python')
>>> bolumler = liste[0], liste[-1], liste[1:5]
>>> bolumler
('P', 'n', ['y', 't', 'h', 'o'])
>>> 'birinci=%s, sonuncu=%s, arası=%s' % bolumler
"birinci=P, sonuncu=n, arası=['y', 't', 'h', 'o']"
>>>
```

Daha karmaşık biçimlendirme uygulandığında, iki teknik karmaşıklık açısından benzemektedir, ancak aşağıdakileri daha önce listelenen biçimlendirme yöntemi çağrı eşdeğerleriyle karşılaştırsanız, _**%**_ ifadelerinin biraz daha basit ve daha kısa olma eğiliminde olduğunu görebilirsiniz.

```python
>>> '%-10s = %10s' % ('monty python', 123.4567)
'monty python =   123.4567'
>>> '%10s = %-10s' % ('monty python', 123.4567)
'monty python = 123.4567  '
>>> '%(platform)10s = %(cihaz)-10s' % dict(platform=sys.platform, cihaz='laptop')
'     win32 = laptop    '
>>> '%e, %.3e, %g' % (3.14159, 3.14159, 3.14159)
'3.141590e+00, 3.142e+00, 3.14159'
>>> '%f, %.2f, %06.2f' % (3.14159, 3.14159, 3.14159)
'3.141590, 3.14, 003.14'
>>> '%x, %o' % (255, 255)
'ff, 377'
```

Biçim yöntemi, _**%**_ ifadesinin sahip olmadığı bir dizi gelişmiş özelliğe sahiptir, ancak daha fazla ilgili biçimlendirmenin temelde karmaşıklık açısından bir fark olduğu görülmektedir. Örneğin, aşağıdakiler alan boyutları, gerekçeleri ve çeşitli argüman referans yöntemleri ile her iki teknikle üretilen aynı sonucu gösterir.

```python
>>> import sys
>>> 'Kullandığım {1[cihaz]:<8} {0.platform:>8} platformunda çalışmaktadır.'.format(sys, {'cihaz': 'laptop'})
'Kullandığım laptop      win32 platformunda çalışmaktadır.'
>>> 'Kullandığım %(cihaz)-8s %(platform)8s platformunda çalışmaktadır.' % dict(cihaz='laptop', platform=sys.platform)
'Kullandığım laptop      win32 platformunda çalışmaktadır.
```

Uygulamada, programların böyle referansları önceden kodlama olasılığı, bir dizi ikame verisini önceden oluşturan bir kod yürütmekten daha az olasıdır. Bunun gibi durumları dikkate aldığımızda, biçimlendirme yöntemi ile _**%**_ ifadesi arasındaki karşılaştırma daha da doğrudan olur.

```python
>>> veri = dict(platform=sys.platform, cihaz='laptop')
>>> 'Kullandığım {cihaz:<8} {platform:>8} platformunda çalışmaktadır.'.format(**veri)
'Kullandığım laptop      win32 platformunda çalışmaktadır.'
>>> 'Kullandığım %(cihaz)-8s %(platform)8s platformunda çalışmaktadır.' % veri
'Kullandığım laptop      win32 platformunda çalışmaktadır.'
```

Bölüm 18'de göreceğimiz gibi, buradaki yöntem çağrısındaki \*\* verileri, anahtarlar ve değerler sözlüğünü ayrı ayrı _**"anahtar = değer"**_ anahtar kelime bağımsız değişkenlerine açan özel bir söz dizimidir, böylece biçim dizesindeki ada göre başvurabilirler. İşlev çağrısı araçlarına kaçınılmaz olarak ileriye dönük bir kavramsal ileri referans, bu da özellikle yeni başlayanlar için biçimlendirme yönteminin bir başka dezavantajı olabilir.

Her zaman olduğu gibi, Python topluluğu _**%**_ ifadelerin, biçimlendirme yöntemi çağrılarının veya her iki tekniğe sahip bir araç setinin zaman içinde daha iyi olup olmadığına karar verecektir. Sundukları ürün hakkında fikir sahibi olmak için bu teknikleri kendi başınıza deneyin ve daha fazla ayrıntı için Python kütüphane referans kılavuzlarına bakın.

### Neden Biçim Yöntemi Tercih Ediliyor?

Şimdi, her iki biçimlendirme tekniğini karşılaştırmak için yeteri kadar bilgiye sahip olduğumuz için, zaman zaman neden biçim yöntemi varyantını kullanmak isteyebileceğinizi de açıklamak istiyorum.

* % ifadesinin kendisinde bulunmayan bir avuç ekstra özelliğe sahiptir. Ancak % alternatifinde kullanılabilirler.
* Daha esnek değer referans söz dizimine sahiptir.
* İkame değeri referanslarını daha açık hale getirebilir. Ancak bu isteğe bağlı bir durumdur.
* Bir operatöre daha anımsatıcı bir yöntem adı eklenebilir.
* Bir ve birden çok değer için farklı söz dizimine izin vermez.
* Bir ifadenin kullanılamayacağı yerlerde, biçim yöntemi kullanılabilir.

Her iki teknik de bugün mevcut olsa ve biçimlendirme ifadesi hala yaygın olarak kullanılmasına rağmen, biçim yöntemi nihayetinde popülerlik kazanabilir ve gelecekte Python geliştiricilerinden daha fazla dikkat çekebilir. Ayrıca, dilde hem ifade hem de yöntemle, karşılaşacağınız kod parçasında görünebilir, böylece her ikisini de anlamanız, öğrenmeniz gerekir. Ancak seçim şu anda yeni kod parçası geliştirmek için hala sizin olduğundan, bu konuyla ilgili bölümü tamamlamadan önce kısaca örnekleri genişletelim.

### Ekstra özellikler: Genel tekniklere karşı özel durumlar

Yöntem çağrısı, Python 2.7 ve 3.1 sürümlerinden itibaren; ikili tür kodları ve binlerce gruplama gibi ifadenin desteklemediği bazı ekstraları destekler. Gördüğümüz gibi, biçimlendirme ifadesi genellikle aynı efektleri başka şekillerde de elde edebilir. İkili biçimlendirme için durum şu şekildedir;

```python
>>> '{0:b}'.format((2 ** 16) - 1)       # İkili kod biçiminde ifade
'1111111111111111'er
>>> '%b' % ((2 ** 16) - 1)      #%b karakter biçimi desteklenmez
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: unsupported format character 'b' (0x62) at index 1
>>> bin((2 ** 16) - 1)  # Ancak diğer genel seçeneklerde işe yarar
'0b1111111111111111'
>>> '%s' % bin((2 ** 16) - 1)   # Hem ifade hem de yöntemde kullanılabilir
'0b1111111111111111'
>>> '{}'.format(bin((2 ** 16) - 1))     # Python 2.7 ve 3.1 sürümlerinden itibaren
'0b1111111111111111'
>>> '%s' % bin((2 ** 16) - 1)[2:]       # Nesneyi dilimleme
'1111111111111111'
```

Önceki not; genel işlevlerin, biçim yönteminin binlerce gruplama seçeneği için benzer şekilde kullanılabildiğini ve özelleştirmeyi tam olarak destekleyebildiğini göstermektedir. Bu durumda, 8 satırlı basit bir yeniden kullanılabilir işlev, ekstra özel durum söz dizimi olmadan bize aynı sonucu üretebilir.

```python
>>> '{:,d}'.format(999999999999)        # Python 2.7 ve 3.1 sürümlerinden itibaren yeni str.format metodu
'999,999,999,999'
>>> '%s' % commas(999999999999)         # Ancak % ile aynı sonuç üretilebilir
'999,999,999,999'
```

{% hint style="info" %}
Daha fazla virgül karşılaştırması için önceki notu inceleyin. Bu, ikili biçimlendirme için önceki örnek ile aynıdır, ancak buradaki _**commas**_ \(virgül\) _isimli işlev kullanıcı tanımlıdır, yerleşik değildir._ Bu nedenle, bu teknik; önceden kodlanmış araçlardan veya tek bir amaç için eklenen özel söz diziminden çok daha genel bir amaçtır.
{% endhint %}

Bu durum, belki de, Python'da \(ve genel olarak betik dosyası dillerinde\) genel durum tekniklerinden ziyade özel durumdaki araçlara daha fazla güvenme eğilimi olduğunu göstermektedir. Bazıları için, programcıların virgül eklemek için bir algoritma geliştirmesi, yazılım geliştirmeyi son kullanıcı için özel olarak görmedikçe haklı çıkarmak mümkün değildir.

Bu felsefi tartışmayı burada bir kenara bırakacağız, ancak pratikte bu durumda eğilimin net etkisi, hem öğrenmeniz hem de hatırlamanız gereken ekstra söz dizimidir. Alternatifleri göz önüne alındığında, yöntemlerin bu ekstra özelliklerinin kendi başlarına belirleyici olacak kadar zorlayıcı olduğu açık değildir.

### Esnek referans söz dizimi: Ekstra karmaşıklık ve işlevsel kesişim

Yöntem çağrısı ayrıca, bazıları daha esnek olarak görülebilecek anahtar ve öznitelik referanslarını doğrudan destekler. Ancak daha önceki örneklerde _**%**_ ifadesindeki sözlük tabanlı biçimlendirmeyi biçim yönteminin anahtar ve öznitelik başvurularıyla karşılaştıracağımız gibi, ikisi genellikle bu temelde bir tercih gerektirmeyecek kadar benzerdir. Örneğin, her ikisi de aynı değere birden çok kez başvurabilir.

```python
>>> '{isim} {isi} {isim}'.format(isim='Guido', isi='Yazılımcı')
'Guido Yazılımcı Guido'
>>> '%(isim)s %(isi)s %(isim)s' % dict(isim='Guido', isi='Yazılımcı')
'Guido Yazılımcı Guido'
```

Özellikle ortak uygulamada, ifade aynı derecede basit veya bazı durumlarda daha basit görünür.

```python
>>> d = dict(isim='Guido', isi='Yazılımcı')
>>> '{0[isim]} {0[isi]} {0[isim]}'.format(d)    # Yöntem, anahtar referans
'Guido Yazılımcı Guido'
>>> '{isim} {isi} {isim}'.format(**d)   # Yöntem, sözlüğü argüman olarak gönderme
'Guido Yazılımcı Guido'
>>> '%(isim)s %(isi)s %(isim)s' % d     # İfade, anahtar referans
'Guido Yazılımcı Guido'
```

Adil olmak gerekirse, yöntemin daha da özel ikame söz dizimi vardır ve diğer karşılaştırmalar her iki şemayı da küçük şekillerde destekleyebilir. Ancak üst üste binme ve ekstra karmaşıklık göz önüne alındığında, biçim yönteminin faydasının ya tek seferlik ya da kullanım durumlarına göre olduğu iddia edilebilir. En azından, şimdi her iki aracı da bilmesi gerekebilecek Python programcıları üzerindeki ek kavramsal yük açıkla haklı görünmemektedir.

### Açık değer referansları: Artık isteğe bağlı ve kullanılması muhtemel değil

Biçim yönteminin en azından tartışmalı bir şekilde daha açık olduğu bir kullanım durumu, biçim dizesine ikame edilecek birçok değer olduğundandır. Örneğin, bölüm 31'de karşılaşacağımız _**lister.py**_ sınıfları örneği, altı öğeyi tek bir dizeye koyar ve bu durumda yöntemin {i} konum etiketlerini okumak, ifadenin _**%s**_'sinden biraz daha kolay görünür.

```text
'\n%s<Class %s, address %s:\n%s%s%s>\n' % (...) # İfade
'\n{0}<Class {1}, address {2}:\n{3}{4}{5}>\n'.format(...) # Yöntem
```

Öte yandan, _**%**_ ifadelerinde sözlük anahtarlarının kullanılması bu farkın çoğunu azaltabilir. Bu aynı zamanda karmaşıklığı biçimlendirmek için en kötü durum senaryosudur ve pratikte çok yaygın değildir; daha tipik kullanım durumları eşit durumdadır. Ayrıca, Python 3.1 ve 2.7 sürümlerinden itibaren, ikame hedeflerinin numaralandırılması konuma göre isteğe bağlı hale gelir ve bu söz konusu faydanın potansiyel olarak tamamen altüst olmasıdır.

```python
>>> 'Monty {0} and the {1} {2}'.format('Python', 'Flying', 'Circus')    # Python 2.6 ve 3.X sürümlerinden itibaren
'Monty Python and the Flying Circus'
>>> 'Monty {} and the {} {}'.format('Python', 'Flying', 'Circus')       # Python 2.7 ve 3.1 sürümlerinden itibaren
'Monty Python and the Flying Circus'
>>> 'Monty %s and the %s %s' % ('Python', 'Flying', 'Circus')   # Tüm Python sürümlerinde
'Monty Python and the Flying Circus'
```

Özlü olması göz önüne alındığında, bunların ikincisi, birincisine göre tercih edilir, ancak yöntemin avantajlarının bir kısmının kullanılmayacağını görülmektedir. Örneğin, kayan nokta biçimlendirmesi üzerindeki etkisi karşılaştırıldığında, biçimlendirme ifadesi hala daha özlü ve daha az dağınık görünmektedir.

```python
>>> '{0:f}, {1:.2f}, {2:05.2f}'.format(3.14159, 3.14159, 3.14159)
'3.141590, 3.14, 03.14'
>>> '{:f}, {:.2f}, {:06.2f}'.format(3.14159, 3.14159, 3.14159)
'3.141590, 3.14, 003.14'
>>> '%f, %.2f, %06.2f' % (3.14159, 3.14159, 3.14159)
'3.141590, 3.14, 003.14'
```

### İsimlendirilmiş yöntem ve bağlamdan bağımsız argümanlar: Estetik, pratikliğe karşı

Biçimlendirme yöntemi ayrıca, _**%**_ operatörünü daha anımsatıcı bir biçim yöntemi adıyla değiştirmede ve tekli veya çoklu ikame değerleri arasında ayrım yapmama avantajını da elde eder. Birincisi, yöntemin ilk bakışta yeni başlayanlar için daha basit görünmesini sağlayabilir \(_**"format"**_ ayrıştırılması birden çok "%" karakterinden daha kolay olabilir\), ancak bu muhtemelen okuyucudan okuyucuya değişir ve küçük görünmektedir.

Bazıları ikinci farkı daha anlamlı görebilir. Biçim ifadesiyle, tek bir değer kendiliğinden verilebilir, ancak birden çok değer için bir _**tuple**_ kullanılmalıdır.

```python
>>> '%.2f' % 1.2345     # Tek değer
'1.23'
>>> '%.2f %s' % (1.2345, 99)    # Çoklu değer
'1.23 99'
```

Teknik olarak biçimlendirme ifadesi, tek bir ikame değerini, bir veya daha fazla öğenin bir _**tuple**_ nesnesini kabul eder. Sonuç olarak, tek bir öğe tek başına veya bir _**tuple**_ nesnesi içinde verilebileceğinden, biçimlendirilecek bir _**tuple**_ nesnesi iç içe geçmiş bir _**tuple**_ nesnesi olarak sağlanmalıdır.

```python
>>> '%s' % 1.23         # Tek değer, kendisi
'1.23'
>>> '%s' % (1.23,)      # Tek değer, tuple içinde
'1.23'
>>> '%s' % ((1.23,),)   # Tek bir tuple, tuple içinde
'(1.23,)'
```

Biçimlendirme yöntemi ise, birden çok değer için bir _**tuple**_ veya bir _**tuple**_ olan tek bir değer yerine, her iki durumda da yalnızca genel işlev bağımsız değişkenlerini kabul ederek bunu sağlar.

```python
>>> '{0:.2f}'.format(1.2345)    # Tek değer
'1.23'
>>> '{0:.2f} {1}'.format(1.2345, 99)    # Çoklu değer
'1.23 99'
>>> '{0}'.format(1.23)          # Tek değer, kendisi
'1.23'
>>> '{0}'.format((1.23,))       # Tek değer, tuple içinde
'(1.23,)'
```

Sonuç olarak, yöntem yeni başlayanlar için daha az kafa karıştırıcı olabilir ve daha az programlama hatasına neden olabilir. Bununla birlikte, bu oldukça küçük bir sorun gibi görünüyor; her zaman bir _**tuple**_ içine değerler koyarsanız ve konulmamış seçeneği yok sayarsanız, ifade esas olarak burada yöntem çağrısı ile aynıdır. Ayrıca yöntem, kısıtlı kullanım moduna ulaşmak için şişirilmiş kod oluşturur. İfadenin Python'un geçmişi üzerinde geniş kullanımı göz önüne alındığında, bu sorun pratik olmaktan daha teorik olabilir ve mevcut kodun, güncellenmesi amaçlanana kadar benzer yeni bir araca taşınmasını haklı göstermeyebilir.

### İfadelere karşı işlevler: Küçük bir kolaylık

Biçim yöntemi için son gerekçe; bir ifadenin görünemediği durumlarda ortaya çıkabilen bir işlevdir. İşlevler hakkında henüz kitapta bu noktada sahip olduğumuzdan daha fazla bilgi gerektirir, bu nedenle burada üzerinde durmayacağız. Hem _**"str.format"**_ yönteminin hem de biçim yerleşik işlevinin diğer işlevlere geçirilebileceğini, başka nesnelerde depolanabileceğini ve bu gibi şeyler söylenmesine yeter. _**%**_ gibi bir ifade doğrudan olamaz, ancak bu dar görüşlülük olabilir, herhangi bir ifadeyi aynı özelliklere sahip bir işleve dönüştürmek için bir satırlık bir _**def**_ kısmi satır _**lambda**_'ya bir döndürmek önemsizdir.

```python
>>> def bicim_metodu(bicim, args): return bicim % args  # Bölüm 4'e bakın.
...
>>> bicim_metodu('%s %s', (88, 89))     # İşlevi çağırıyoruz ve nesneleri argüman olarak gönderiyoruz
'88 89'
>>> str.format('{} {}', 88, 89)         # Yerleşik işlev
'88 89'
>>> diger_metod(bicim_metodu)   # Başka bir işleve parametre olarak gönderilebilir
```

Sonunda, bu bir seçenek olmayabilir. İfade Python kodunda hala daha yaygın gibi görünse de, bugün hem biçimlendirme ifadeleri hem de yöntemler Python'da kullanılabilir ve çoğu programcı gelecek yıllarda her iki tekniği de kullanarak fayda sağlayacaktır. Bu, dile yeni gelenlerin çalışmalarını bu bölüm için ikiye katlayabilir, ancak açık kaynak yazılım dünyası olarak adlandırdığımız bu fikir ortamında her zaman daha fazlasına yer var gibi görünmektedir.

## Genel Tür Kategorileri

Bu bölümde Python'un koleksiyon nesnelerinin ilkini, dizeyi inceledik, buradan baktığımız türlerin çoğuna uygulanacak birkaç gelen tür kavramını tanımlayarak bu bölümü kapatalım. Yerleşik türlerle ilgili olarak, işlemlerin aynı kategorideki tüm türler için aynı şekilde çalıştığı ortaya çıkmaktadır, bu nedenle bu fikirlerin çoğunu yalnızca bir kez tanımlamamız gerekecektir. Şimdiye kadar sadece sayıları ve dizeleri inceledik, ancak Python'daki üç ana tür kategoriden ikisini temsil ettikleri için, düşündüğünüzden başka birkaç tür hakkında daha fazla şey biliyorsunuz.

### Kategorilere göre; türlerin paylaştıkları işlem kümeleri

Öğrendiğiniz gibi, dizeler değişmez dizilerdir; yerinde değiştirilemezler \(_**immutable**_\) ve bunlar indeks tarafından erişilen konumlandırılmış sıralı koleksiyonlardır. Kitabın bu bölümünde çalışacağımız tüm sekanslar, dizede çalışma sırasında bu dizide gösterilen sekans işlemlerine \(birleştirme, indeksleme, yineleme vb.\) yanıt vermektedir. Daha resmi olarak, Python'da bu genel niteliğe sahip üç ana tür \(ve işlem\) kategorisi vardır.

* Sayılar \(tam sayı, kayan nokta, ondalık, kesir ve diğerleri\); toplama, çarpma, çıkarma vb. işlemleri destekler.
* Diziler \(dize, liste, tuple\); indeksleme, dilimleme, birleştirme vb. işlemleri destekler.
* Eşlemeler \(sözlükler\); anahtar vb. ile indeksleme işlemini destekler.

Bu bölümün başında burada genel _**"dizeler"**_ etiketi altında bahsettiğim Python 3.X bayt dizeleri ve 2.X _**unicode**_ dizelerini \(bölüm 37\) ekliyorum. Kümeler kendi başlarına bir kategoridir \(anahtarları değerlerle eşlemezler ve konumsal olarak sıralı diziler değildir\) ve henüz derinlemesine kümeleri incelemedik. Bunu bir sonraki bölümde gerçekleştireceğiz. Bununla birlikte, karşılaşacağımız diğer türlerin çoğu sayılara ve dizelere benzer. Örneğin; _**x**_ ve _**y**_ dizi nesneleri için;

* x + y, her iki işlenenin içeriğiyle yeni bir dizi nesnesi oluşturur.
* x \* n, dizi işleneni x'in n kadar kopyasına sahip yeni bir dizi nesnesi oluşturur.

Başka bir deyişle, bu işlemler _**dizeler**_, _**listeler**_, _**tuple**_ ve bazı kullanıcı tanımlı nesne türleri de dahil olmak üzere her türlü dizide aynı şekilde çalışır. Tek fark, geri aldığınız yeni sonuç nesnesinin _**x**_ ve _**y**_ işlenenleriyle aynı türde olmasıdır; listeleri birleştirirseniz, dize değil yeni bir liste elde edersiniz. Dizin oluşturma, dilimleme ve diğer dizi işlemleri de tüm dizilerde aynı şekilde çalışır; işlenen nesnenin türü, Python'a görevin hangi şekilde yerine getirileceğini belirtir.

### Değişken Türler Yerinde Değiştirilebilirler

Değişmez sınıflandırma, farkında olmak için önemli bir kısıtlamadır, ancak yeni kullanıcıları için durumu karmaşıklaştırma eğilimindedir. Bir nesne türü değiştirilemezse, değerini yerinde değiştiremezsiniz; bunu denerseniz Python bir hata oluşturur. Bunun yerine, yeni değeri içeren yeni bir nesne oluşturmak için kodu çalıştırmanız gerekir. Python'daki ana çekirdek türleri şu şekilde parçalanır.

* Değişmezler \(sayılar, dizeler, tuple, fronzensets\); değiştirilemez kategorideki nesne türlerinin hiçbiri yerinde değişiklikleri desteklemese de, yeni nesneler yapmak ve sonuçlarını değişkenlere gerektiği gibi atamak için her zaman ifadeleri çalıştırabiliriz.
* Değişebilenler \(listeler, sözlükler, kümeler, bytearray\); tersine, değişebilir türler her zaman yeni nesneler oluşturmayan işlemlerle değiştirilebilir. Bu tür nesneler kopyalanabilse de, yerinde değişiklikler doğrudan değişikliği destekler.

{% hint style="info" %}
Genel olarak, değişmez türler, bir nesnesin bir programın başka bir bölümü tarafından değiştirilmeyeceğini garanti ederek bir dereceye kadar bütünlük sağlar. Bunun neden önemli olduğu hakkında daha fazla bilgi için bölüm 6'daki paylaşılan nesne referansları konusuna bakın.
{% endhint %}

Listelerin, sözlüklerin ve grupların tür kategorilerine nasıl katıldığını görmek için bir sonraki bölüme geçmemiz gerekiyor.

## Bölüm Özeti

Bu bölümde dize nesnesi türünü derinlemesine inceledik. Dize değişmezlerini kodlamayı öğrendik ve dizi ifadeleri, dize yöntemi çağrıları ve hem ifadeler hem de yöntem çağrılarıyla dize biçimlendirme dahil dize işlemlerini gözlemledik. Bölüm boyunca, dilimleme, yöntem çağrısı söz dizimi ve üç tırnaklı blok dizeleri gibi çeşitli kavramları derinlemesine gördük. Ayrıca, çeşitli türlerde ortak olan bazı temel fikirleri tanımladık; örneğin, diziler tüm işlem kümesini paylaşır.

Bir sonraki bölümde, türler turumuza, Python'daki listeler ve sözlüklerdeki en genel nesne koleksiyonlarına göz atarak devam edeceğiz. Bulduğunuz gibi, burada öğrendiklerinizin çoğu bu türler için de geçerli olacaktır. Daha önce de belirtildiği gibi, bu kitabın son bölümünde, Python programcılarının ilgisini çeken bazılarını ilgilendiren _**Unicode**_ metin ve ikili verilerin ayrıntılarını ortaya çıkarmak için Python'un dize modeline döneceğiz. Yine de devam etmeden önce, burada ele alınan materyali gözden geçirmek için başka bir bölüm sınavı yapalım.

### Bölüm sonu sınavı

1. Dize bulma yöntemi \(_**find**_\) bir listeyi aramak için kullanılabilir mi?
2. Bir dize dilimi ifadesi bir listede kullanılabilir mi?
3. Bir karakteri ASCII tam sayı koduna nasıl dönüştürürdünüz? Tam sayıdan karaktere nasıl başka bir yol ile dönüştürebilirsiniz?
4. Python'da bir dizeyi nasıl değiştirebilirsiniz?
5. "s,p,a,m" değerine sahip bir s dizesi verildiğinde, ortadaki iki karakteri ayıklamanın iki yolunu belirtin.
6. "a\nb\x1f\000d" dizesinde kaç karakter vardır?
7. Neden _**string**_ yöntemi çağrıları yerine _**string**_ modülünü kullanabilirsiniz?

### Bölüm sonu sınavı, cevapları

1. Hayır, çünkü yöntemler her zaman türe özgüdür; yani, yalnızca tek bir veri türü üzerinde çalışırlar. _**x + y**_ gibi ifadeler ve _**len\(x\)**_ gibi yerleşik işlevler geneldir ve çeşitli türlerde çalışabilir. Bu durumda; örneğin, _**in**_ ifadesi dize bulmaya \(_**find**_\) benzer bir etkiye sahiptir, ancak hem dizeleri hem de listeleri aramak için kullanılabilir. Python 3.x'te yöntemleri kategorilere göre gruplama girişimi vardır.
2. Evet, yöntemlerin aksine, ifadeler geneldir ve birçok türe uygulanır. Bu durumda; dilim ifadesi gerçekten bir dizi işlemidir. Dizeler, listeler ve _**tuple**_ dahil olmak üzere herhangi bir dizi nesnesi üzerinde çalışır. Tek fark, bir listeyi dilimlediğinizde yeni bir liste geri dönmesidir.
3. Yerleşik _**ord\(s\)**_ işlevi, tek karakterli bir dizeden bir tam sayı karakter koduna dönüştürülür; _**chr\(i\)**_ tam sayı kodundan bir dizgeye dönüşür. Bununla birlikte, bu tam sayıların yalnızca karakterleri, ASCII karakter kümesinden çizilen metin için ASCII kodları olduğunu unutmayın. _**Unicode**_ modelinde, metin dizeleri gerçekten de _**Unicode**_ kod noktası tam sayılarını belirleyen dizelerdir ve ASCII tarafından ayrılmış 7 bitlik sayı aralığının dışında kalabilir.
4. Dizeler değiştirilemez, değişmezler. Ancak, benzer bir etkiyi birleştirerek, dilimleyerek,  biçimlendirme ifadelerini çalıştırarak veya _**replace**_ gibi bir yöntem çağrısı kullanarak ve ardından sonucu orijinal değişken adına geri atayarak benzer bir etkiyi elde edebilirsiniz.
5. _**s\[2:4\]**_ kullanarak dizeyi dilimleyebilir veya virgül üzerinde bölebilir ve _**s.split\(','\)\[1\]**_ kullanarak dizeyi dizine ekleyebilirsiniz. Kendiniz görmek için bunları etkileşimli olarak deneyin.
6. Cevap altı. "a\nb\x1f\000d" dizesi a, yeni satır \(\n\), b, ikili 31 \(onaltılık bir kaçış \x1f\), ikili 0 \(bir sekizli kaçış \000\) ve d karakterlerini içerir. Bunu doğrulamak için dizeyi yerleşik _**len**_ işlevine iletin ve gerçek kod noktası değerlerini görmek için karakterinin _**ord**_ sonuçlarının her birini yazdırın. Kaçışlar hakkında daha fazla bilgi için tablo 7-2'ye bakın.
7. Bugün _**string**_ nesnesi yöntemi çağrıları yerine asla _**string**_ modülünü kullanmamalısınız. Bu kullanımdan kaldırılmıştır ve çağrıları Python 3.X'te tamamen kaldırılmıştır. Dize modülünü bugün kullanmanın tek geçerli nedeni, önceden tanımlanmış sabitler gibi diğer araçlardır. Ayrıca şimdi çok eski ve tozlu Python kodunda göründüğünü bilmelisiniz.
