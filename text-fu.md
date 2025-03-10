## 1 stdout (Standart Çıkış)**

Şimdiye kadar birçok komuta ve bunların çıktılarına aşina olduk ve bu da bizi bir sonraki konumuz olan I/O (giriş/çıkış) akışlarına getiriyor. Aşağıdaki komutu çalıştıralım ve bunun nasıl çalıştığını tartışalım.

    $ echo Hello World >peanuts.txt


Az önce ne oldu? Bu komutu çalıştırdığınız dizini kontrol edin ve bakın, peanuts.txt adında bir dosya göreceksiniz, bu dosyanın içine bakın ve Hello World metnini göreceksiniz. Tek bir komutta pek çok şey oldu, o yüzden şimdi bunlan inceleyelim.

    $ echo Hello World

Bunun ekrana Merhaba Dünya yazdırdığını biliyoruz, ama nasıl? Süreçler girdi almak ve çıktı döndürmek için G/C akışlarını kullanır. Varsayılan olarak echo komutu girdiyi (standart girdi veya stdin) klavyeden alır ve çıktıyı (standart çıktı veya stdout) ekrana döndürür. Bu yüzden kabuğunuza echo Hello World yazdığınızda ekranda Hello World'u görürsünüz. Ancak, G/Ç yönlendirmesi bu varsayılan davranışı değiştirmemize izin vererek bize daha fazla dosya esnekliği sağlar.

Komutun bir sonraki bölümüne geçelim:

    $ echo Hello World>>peanuts.txt


Bu, peanuts.txt dosyasının sonuna Hello World ekleyecektir, eğer dosya zaten mevcut değilse, > yönlendiricisinde yaptığı gibi bizim için oluşturacaktır!Ancak, eğer mevcutsa üzerine yazacaktır (kullandığınız kabuğa bağlı olarak bunu önlemek için bir kabuk bayrağı ekleyebilirsiniz). Ve stdout yönlendirmesi temel olarak bu şekilde çalışır!

Diyelim ki peanuts.txt dosyamın üzerine yazmak istemedim, neyse ki bunun için de bir yönlendirme operatörü var, >>:

## 2 stdin (Standart Giriş)

Bir önceki dersimizde dosya ya da ekran gibi kullanabileceğimiz farklı stdout akışları olduğunu öğrenmiştik. Aynı zamanda kullanabileceğimiz farklı standart girdi (stdin) akışları da vardır. Klavye gibi aygıtlardan gelen stdin olduğunu biliyoruz, ancak dosyaları, diğer işlemlerden gelen çıktıları ve terminali de kullanabiliriz, bir örnek görelim.

Bu örnek için bir önceki dersteki peanuts.txt dosyasını kullanalım, içinde Hello World metni olduğunu hatırlayın.

    $ cat < peanuts.txt > banana.txt


Tıpkı stdout yönlendirmesi için > kullandığımız gibi, stdin yönlendirmesi için de < kullanabiliriz.

Normalde cat komutuna bir dosya gönderirsiniz ve bu dosya stdin olur, bu durumda biz peanuts.txt dosyasını stdin olarak yönlendirdik. Daha sonra cat peanuts.txt dosyasının çıktısı olan Hello World, banana.txt adlı başka bir dosyaya yönlendirilir.

## 3 stderr (Standart Hata)

Şimdi biraz farklı bir şey deneyelim, sisteminizde var olmayan bir dizinin içeriğini listelemeye çalışalım ve çıktıyı tekrar peanuts.txt dosyasına yönlendirelim.

    $ Is/fake/directory > peanuts.txt

Görmeniz gereken şey şu:

    ls: cannot access /fake/directory: No such file or directory

Şimdi muhtemelen bu mesajın dosyaya gönderilmesi gerekmez miydi diye
düşünüyorsunuzdur. Aslında burada standart hata (stderr) adı verilen başka bir G/Ç akışı vardır. Varsayılan olarak, stderr çıktısını ekrana da gönderir, stdout'tan tamamen farklı bir akımdır. Bu yüzden çıktısını farklı bir şekilde yönlendirmeniz gerekir.

Ne yazık ki yönlendirici < veya > kullanmak kadar güzel değil ama oldukça yakın. Dosya tanımlayıcıları kullanmamız gerekecek. Bir dosya tanımlayıcısı, bir dosyaya veya akışa erişmek için kullanılan negatif olmayan bir sayıdır. Bu konuyu daha sonra derinlemesine inceleyeceğiz. ancak şimdilik stdin, stdout ve stderr için dosya tanımlayıcısının sırasıyla 0, 1 ve 2 olduğunu bilin.

Yani şimdi stderr ımızı dosyaya yönlendirmek istiyorsak bunu yapabiliriz:

    $ Is /fake/directory 2> peanuts.txt

peanuts.txt dosyasında sadece stderr mesajlarını görmelisiniz.

Peki ya peanuts.txt dosyasında hem stderr hem de stdout mesajlarını görmek istersem? Bunu dosya tanımlayıcıları ile de yapmak mümkündür.

    $ 1s /fake/directory > peanuts.txt 2>&1

Bu. Is/fake/directory sonuçlarını peanuts.txt dosyasına gönderir ve ardından stderr'yi 2>&1 aracılığıyla stdout'a yönlendirir. Buradaki işlem sırası önemlidir. 2>&1 stderr'yi stdout'un

İşaret ettiği yere gönderir. Bu durumda stdout bir dosyaya işaret ediyor, bu yüzden 2>&1 stderr'ı da bir dosyaya gönderir. Yani eğer peanuts.txt dosyasını açarsanız hem stderr hem de stdout'u görmeniz gerekir. Bizim durumumuzda, yukarıdaki komut sadece stderr çıktısı verir.

Hem stdout hem de stderr'yi bir dosyaya yönlendirmenin daha kısa bir yolu vardır:

    $ 1s /fake/directory > peanuts.txt

Peki ya bu saçmalıkların hiçbirini istemiyorsam ve stderr mesajlarından tamamen kurtulmak İstiyorsam? Çıktıyı /dev/null adlı özel bir dosyaya da yönlendirebilirsiniz ve bu dosya tüm girdileri atacaktır.

    $ ls /fake/directory 2> /dev/null

## 4 Pippe ve tee

Şimdi biraz tesisat işlerine girelim, tam olarak değil ama biraz. Bir komut deneyelim:

    $ ls -la/etc

Çok uzun bir öğe listesi görmelisiniz, aslında okuması biraz zor. Bu çıktıyı bir dosyaya yönlendirmek yerine, çıktıyı less gibi başka bir komutta görebilsek güzel olmaz mıydı? Yapabiliriz!

    $ ls -la/etc less

Dikey bir çubukla temsil edilen boru operatörü, bir komutun stdout'unu almamızı ve bunu başka bir işlemin stdin'i yapmamızı sağlar. Bu durumda, Is -la /etc komutunun stdout'unu aldık ve daha sonra bunu less komutuna pipetledik. Pipe komutu son derece kullanışlıdır ve onu sonsuza kadar kullanmaya devam edeceğiz.

Peki ya komutumun çıktısını iki farklı akışa yazmak istersem? Bu tee komutu ile mümkündür.

    $ Is | tee peanuts.txt

Ekranınızda is çıktısını görmelisiniz ve peanuts.txt dosyasını açarsanız aynı bilgileri görmelisiniz!

çeviri: yusuf ali karkoç

kontrol: şevval dermir

## 5 env (Environment)*
Aşağıdaki komutu çalıştırın:

    $ echo $HOME 

Ev dizininizin yolunu görmelisiniz, benimki /home/pete gibi görünüyor.

Peki ya bu komut?

    $ echo $USER  

Kullanıcı adınızı görmelisiniz!

Bu bilgiler nereden geliyor? Çevre değişkenlerinizden geliyor. Bunları görmek için

    $ env  

yazabilirsiniz. Bu komut, şu anda ayarlanmış çevre değişkenleriniz hakkında oldukça fazla bilgi çıktısı verir. Bu değişkenler, kabuk ve diğer işlemler tarafından kullanılabilecek yararlı bilgiler içerir.

İşte kısa bir örnek:

    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/bin
    PWD=/home/user
    USER=pete

Özellikle önemli bir değişken PATH Değişkeni'dir. Bu değişkenlere, değişken adının önüne bir $ ekleyerek erişebilirsiniz:

    $ echo $PATH
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/bin

Bu komut, sisteminizin bir komut çalıştırırken aradığı ve iki nokta üst üste ile ayrılmış bir dizi yolu döndürür. Diyelim ki internetten bir paket indirip manuel olarak standart olmayan bir dizine kurdunuz ve bu komutu çalıştırmak istiyorsunuz, $ coolcommand yazdığınızda terminal "komut bulunamadı" şeklinde bir hata verir. Bu saçma çünkü o binary'yi bir klasörde görüyorsunuz ve var olduğunu biliyorsunuz. Olan şey, $PATH değişkeninin bu dizini aramıyor olması ve bu yüzden hata veriyor.

Diyelim ki o dizinden çalıştırmak istediğiniz tonlarca binary var, o zaman sadece PATH değişkenini değiştirerek o dizini PATH çevre değişkeninize ekleyebilirsiniz.

** 6. cut**

CUT Metni işlemek için kullanabileceğiniz birkaç faydalı komut öğreneceğiz. Başlamadan önce, üzerinde çalışacağımız bir dosya oluşturalım. Aşağıdaki komutu kopyalayıp yapıştırın, sonra lazy ve dog arasına bir TAB ekleyin (Ctrl-v + TAB tuşlarına aynı anda basarak).

    $ echo 'The quick brown; fox jumps over the lazy dog' > sample.txt

İlk öğreneceğimiz komut cut komutudur. Bu komut, bir dosyadan metin parçalarını çıkarır.

`Karakterlere göre içerik çıkarmak için:

    $ cut -c 5 sample.txt 

 
Bu komut, dosyanın her satırındaki 5. karakteri çıkarır. Bu durumda "q" karakterini görmelisiniz, dikkat edin, boşluk da bir karakter olarak sayılır.

Bir alana göre içerik çıkarmak için, biraz değişiklik yapmamız gerekecek:

    $ cut -f 2 sample.txt  

-f veya alan (field) bayrağı, metni alanlara göre keser. Varsayılan olarak, TAB karakterlerini ayırıcı olarak kullanır, yani TAB ile ayrılmış her şey bir alan olarak kabul edilir. Çıktınızda "dog" kelimesini görmelisiniz.

Özelleştirilmiş bir ayırıcı ile içerik çıkarmak için, alan bayrağını ayırıcı bayrağı ile birleştirebilirsiniz:`

    $ cut -f 1 -d ";" sample.txt  

Bu komut, TAB ayırıcısını ";" ayırıcısına değiştirir ve birinci alanı kestiğimiz için sonuç "The quick brown" olmalıdır.

## 7 paste

PASTE komutu, cat komutuna benzer, bir dosyadaki satırları birleştirir. Aşağıdaki içeriğe sahip yeni bir dosya oluşturalım:

    sample2.txt
    The
    quick
    brown
    fox

Şimdi tüm bu satırları tek bir satıra birleştirelim:

    $ paste -s sample2.txt  

Varsayılan ayırıcı paste komutu için TAB'dır, bu yüzden şimdi her kelime arasında TAB'lar bulunan tek bir satır olmalıdır.

Bu ayırıcıyı (-d) daha okunabilir bir şeye değiştirelim:

    $ paste -d ' ' -s sample2.txt 

 
Şimdi her şey bir satırda olmalı ve kelimeler arasında boşluklarla ayrılmalıdır.

ŞEVVAL DEMİR TARAFINDAN ÇEVRİLDİ
YUSUF ALİ KARAKOÇ TARAFINDAN KONTROL EDİLMİŞTİR.

## 8 head
Farz edelim ki çok uzun bir dosyamız var, aslında seçebileceğimiz pek çok dosya da mevcut, `cat /var/log/syslog` komutunu yazalım. Sayfalar dolusu metin görmelisiniz. Peki, bu metin dosyasının sadece ilk birkaç satırını görmek istersem ne yapmalıyım? Bunu `head` komutu ile yapabiliriz, varsayılan olarak `head` komutu dosyadaki ilk 10 satırı gösterir.

    $ head /var/log/syslog
    
Satır sayısını da istediğiniz şekilde değiştirebilirsiniz, diyelim ki ben bunun yerine ilk 15 satırı görmek istedim.

    $ head -n 15 /var/log/syslog
    
The -n flag stands for number of lines.

## 9 tail
head komutuna benzer şekilde tail komutu da varsayılan olarak bir dosyanın son 10 satırını görmenizi sağlar.

    $ tail /var/log/syslog
    
Head komutunda olduğu gibi, görmek istediğiniz satır sayısını değiştirebilirsiniz.

    $ tail -n 10 /var/log/syslog
    
Kullanabileceğiniz bir diğer harika seçenek ise -f (follow) bayrağıdır, bu bayrak dosya büyüdükçe onu takip etmenizi sağlar. Bunu deneyin ve ne olduğunu görün.

    $ tail -f /var/log/syslog
    
Syslog dosyanız, sisteminizle etkileşimde bulunurken sürekli değişecektir ve `tail -f` komutunu kullanarak o dosyaya eklenen her şeyi görebilirsiniz.

## 10 expand and unexpand

Cut komutu dersimizde, içinde bir TAB bulunan sample.txt dosyamız vardı. Normalde TAB'lar genellikle belirgin bir fark yaratır, ancak bazı metin dosyaları bunun yeterince iyi şekilde görünmesini sağlamaz. Bir metin dosyasındaki TAB'lar, istediğiniz boşluk düzeni olmayabilir. TAB'ları boşluklara dönüştürmek için `expand` komutunu kullanın.

    $ expand sample.txt
    
Yukarıdaki komut, her TAB'ı bir grup boşluğa dönüştürerek çıktı verecektir. Bu çıktıyı bir dosyada saklamak için aşağıdaki gibi çıktı yönlendirmesini kullanın.

    $ expand sample.txt > result.txt

Expand komutunun tersine, her bir boşluk grubunu bir TAB'a dönüştürmek için `unexpand` komutunu kullanabiliriz:

    $ unexpand -a result.txt

"Çeviren : Ferdi Tekik"
"kontrol eden : befrin bakıcı"


### 11. join and split

Join komutu, birden fazla dosyayı ortak bir alan üzerinden birleştirmenize olanak tanır:

Diyelim ki iki dosyam vardı ve bunları birleştirmek istiyorum:"

    file1.txt
  

>     
>     1 John
>       
>     2 Jane
>       
>     3 Mary
>     
>     file2.txt
>       
>     1 Doe
>       
>     2 Doe
>       
>     3 Sue
>     
>     $ join file1.txt file2.txt
>       
>     1 John Doe
>       
>     2 Jane Doe
>       
>     3 Mary Sue


"Dosyalarımın nasıl birleştirildiğine bakın. Varsayılan olarak, dosyalarım ilk alana göre birleştirildi ve alanların aynı olması gerekiyor. Eğer aynı değillerse, onları sıralayabilirsiniz. Bu durumda dosyalar 1, 2, 3 üzerinden birleştirildi.

Peki, aşağıdaki dosyaları nasıl birleştirirdik?"

    > > file1.txt    John 1    Jane 2    Mary 3
    > 
    > file2.txt    1 Doe    2 Doe    3 Sue

Bu dosyayı birleştirmek için hangi alanları birleştireceğinizi belirtmeniz gerekir, bu durumda dosya1.txt'de alan 2'yi ve dosya2.txt'de alan 1'i istiyoruz, bu nedenle komut şu şekilde görünecektir:

$ join -1 2 -2 1 file1.txt file2.txt

      
    1 John Doe
      
    2 Jane Doe
      
    3 Mary Sue
-1, file1.txt’i, -2 ise file2.txt’i ifade eder. Oldukça pratik. Ayrıca, split komutuyla bir dosyayı farklı dosyalara bölebilirsiniz:

    $ split somefile
    
Bu, farklı dosyalara bölecektir, varsayılan olarak 1000 satır sınırına ulaştıklarında onları bölecektir. Dosyalar varsayılan olarak x** olarak adlandırılır.

### 12. sort

Sort komutu, satırları sıralamak için kullanışlıdır.
file1.txt
  

    dog
      
    cow
      
    cat
      
    elephant
      
    bird
    
    $ sort file1.txt
      
    bird
      
    cat
      
    cow
      
    dog
      
    elephant
    
Ayrıca ters sıralama da yapabilirsiniz:

> $ sort -r file1.txt    elephant    dog    cow    cat    bird

Ayrıca sayısal değere göre de sıralama yapabilirsiniz:

    $ sort -n file1.txt
      
    bird
      
    cat
      
    cow
      
    elephant
      
    dog
    
### 13. tr (Translate)

tr (translate) komutu, bir karakter kümesini başka bir karakter kümesine dönüştürmenizi sağlar. Tüm küçük harfleri büyük harflere dönüştürme örneğini deneyelim.

    $ tr a-z A-Z
      
    hello
      
    HELLO
    
Gördüğünüz gibi a-z aralıklarını A-Z haline getirdik ve küçük harfle yazdığımız tüm metinler büyük harfle yazılıyor.


ÇEVİREN= BEFRİN BAKICI
Kontrol Eden  : FERDİ TEKİK

## 14. uniq (Unique)
"uniq (benzersiz) komutu, metin ayrıştırmak için başka bir kullanışlı araçtır.
Diyelim ki, birçok tekrarı olan bir dosyanız var:"
  

    reading.txt 
    book
    book
    paper
    paper
    article
    article
    magazine
"Ve tekrarları kaldırmak istiyorsanız, uniq komutunu kullanabilirsiniz:"

    $ uniq reading.txt
    book
    paper
    article
    magazine
"Bir satırın kaç kez tekrarlandığını sayalım:"

   

     $ uniq -c reading.txt
    2 book
    2 paper
    2 article
    1 magazine
"Sadece benzersiz değerleri alalım:"
        $ uniq -u reading.txt
    magazine
    "Sadece tekrarlanan değerleri alalım:"
        $ uniq -d reading.txt
    book
    paper
    article
    Not: uniq komutu, tekrar eden satırları yalnızca yan yana olduklarında tespit eder. Örneğin:

Diyelim ki, yan yana olmayan tekrarlar içeren bir dosyanız var:

    reading.txt
    book
    paper
    book
    paper
    article
    magazine
    article
    $ uniq reading.txt
  

    reading.txt
    book
    paper
    book
    paper
    article
    magazine
    article
   

     uniq tarafından döndürülen sonuç, ilk örnekteki gibi sadece benzersiz satırları değil, tüm girdileri içerecektir.

uniq komutunun bu sınırlamasını aşmak için, uniq ile birlikte sort komutunu kullanabiliriz:

    $ sort reading.txt | uniq
    article
    book
    magazine
    paper
    
## 15. wc and nl
wc (kelime sayma) komutu, bir dosyadaki toplam kelime sayısını gösterir.

    $ wc /etc/passwd
     96     265    5925 /etc/passwd
     
     Sırasıyla, satır sayısını, kelime sayısını ve bayt sayısını gösterir.
Belirli bir alanın sadece sayısını görmek için, sırasıyla -l, -w veya -c seçeneklerini kullanabilirsiniz.

    $ wc -l /etc/passwd
    96
    
Bir dosyadaki satır sayısını kontrol etmek için kullanabileceğiniz bir diğer komut ise nl (satır numaralandırma) komutudur.
      
    file1.txt 
    i
    like
    turtles
    $ nl file1.txt
    1. i
    2. like
    3. turtles
    
## 16. grep
grep komutu, muhtemelen kullanacağınız en yaygın metin işleme komutudur. Belirli bir desenle eşleşen karakterleri dosyalarda aramanıza olanak tanır. Diyelim ki, bir dosyanın belirli bir dizinde olup olmadığını öğrenmek istiyorsunuz veya bir dosyada bir stringin bulunup bulunmadığını görmek istiyorsunuz. Elbette her bir satırı tek tek incelemezsiniz, grep kullanırsınız!
Örnek olarak, sample.txt dosyamızı kullanalım:

    $ grep fox sample.txt

grep'in, sample.txt dosyasında "fox" kelimesini bulduğunu görmelisiniz.

Ayrıca, desenleri büyük/küçük harf duyarsız şekilde aramak için -i bayrağını da kullanabilirsiniz:

    $ grep -i somepattern somefile

grep ile daha esnek olmak için, onu diğer komutlarla | (pipe) operatörü ile birleştirebilirsiniz.

    $ env | grep -i User

Gördüğünüz gibi, grep oldukça esnektir. Deseninizde düzenli ifadeleri (regular expressions) bile kullanabilirsiniz.

    $ ls /somedir | grep '.txt$'

Bu, somedir içindeki tüm .txt ile biten dosyaları döndürmelidir.

hazırlayan serhat töre 
şevval demir tarafından kontrol edildi.



