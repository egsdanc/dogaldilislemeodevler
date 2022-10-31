Dersteki örnekden farklı ilk olarak en çok tekrar eden kelimeleri çıkarttım ve tf_idf yaparken
doğru sonuca ulaşmak için daha doğru kelimelerin kullanılmasını sağladım.
for i in range(5):  
  sorted_frequencies[i]=0
yukarıdaki kod ile en çok terar eden ilk 5 kelimeyi çıkarttım  

daha sonra önerilen filmleri yami önceden benzerliğe göre sıraya girmiş
filmlerin türü  eğer istediğimiz film'in türü ile aynı değilse yeniden önermesini sağladım bunun
sebebi sadece özel isimler yüzünden tür'ü bile aynı olmayan dizileri önermesiydi

def most_similar_films(film_adi, benzer_film_sayisi):
    film_indexi = df[df["film adi"] == film_adi].index[0]
    print("Aranan Film:")
    print(film_2_str(film_indexi))
    print()
    print("* " * 30)
    print()
    
    benzerlikler = []
    sorgu_film = filmler_tf_idf[film_indexi]
    for vec in filmler_tf_idf:
        benzerlik = cos_sim(sorgu_film, vec)
        benzerlikler.append(benzerlik)
    
    en_benzer_film_indexleri = (- np.array(benzerlikler)).argsort()[:benzer_film_sayisi + 1]
    print("Benzer Filmler:\n")
    for i, film_idx in enumerate(en_benzer_film_indexleri[1:]):
        print(str(i+1) + ") " + film_2_str(film_idx))
        print()
        print("- " * 20)
        print()

olan kodu aşağıdaki şekilde değiştirdim yani bir for dönügüsü
,if ve yeni diziler kullanarak önerilen filmlerin de türünün yazdığımız film ile
aynı olmasını sağladım ve yaptığım kontroller ile daha doğru sonuçlar aldığımızı gördüm
çünkü bence filmlerin türü aynı değilse
önerilmesi de doğru olmaz. 
 

def most_similar_films(film_adi, benzer_film_sayisi):
    film_indexi = df[df["film adi"] == film_adi].index[0]
    print("Aranan Film:")
    print(film_2_str(film_indexi))
    print()
    print("* " * 30)
    print()

    benzerlikler = []
    sorgu_film = filmler_tf_idf[film_indexi]
    for vec in filmler_tf_idf:
        benzerlik = cos_sim(sorgu_film, vec)
      
        benzerlikler.append(benzerlik)
    k=0
    en_benzer_film_indexleri = (- np.array(benzerlikler)).argsort()[:10]
    for h  in range(10): 
      film = df.iloc[en_benzer_film_indexleri[h]] 
      filmasıl = df.iloc[film_indexi] 
      if film['tur']==filmasıl['tur']: 
        if k<5:
          en_benzer_film_indexleri[k]=en_benzer_film_indexleri[h] 
          k=k+1
    
    print("Benzer Filmler:\n")
    for i, film_idx in enumerate(en_benzer_film_indexleri[1:4]):
        print(str(i+1) + ") " + film_2_str(film_idx))
        print()
        print("- " * 20)
        print()

örnek olarak eski haliyle
Fantastik Canavarlar: Dumbledore'un Sırları[Fantastik,Macera] filmini arattığımızda ilk iki film
tutarlı ancak 3. film olarak The Gambler[dram,gerilimm,polisiye] filmini öneriyordu
yeni haliyle ilk iki film e ek olarak The Gambler film i yerine bir başka fantastik macera
film'i olan Harry Potter Ve ölüm yadigarlarını önerdi 