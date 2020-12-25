# Sorular ve Evliya'nın Cevapları
* [Soru 1](#Aga-yarım-yamalak-oldu-bu)
* [Soru 2](#bir-tane-corner-case-var-onu-çözemedik)
* [Soru 3](#on-numara-çözüm)
* [Soru 4](#üzgünüm)

## Soru 1
1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.
```

SELECT DISTINCT Sport,champ,third,fifth,fq,status,status2, 
FROM(
SELECT  Sport,Country,fq,lead(Country,0) over(partition by sport order by fq DESC) as champ,lead(Country,2) over(partition by sport order by fq DESC) as third,lead(Country,4) over(partition by sport order by fq DESC) as fifth,first_value(Country) over (partition by Sport order by fq Desc ) as Status,first_value(fq) over (partition by Sport order by fq Desc ) as Status2,
FROM(
SELECT Sport,Country,COUNT(*) as fq 
FROM sample.summer_medals a
 WHERE YEAR > 1980
 Group by Sport, Country
 ORDER BY Sport,fq  DESC )) 
```
	
## Soru 2
1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.
```

SELECT DISTINCT * /* aynı sporcuyu bir daha yazdırmaya gerek yok */
FROM(
SELECT Athlete,Sport,first,second,third, /* yıllar 4 sene olarak ardışık mı check et */
CASE
WHEN second = first +4 and third = second + 4 THEN  'Found!' ELSE NULL END As sol
FROM(
SELECT /* Atletlerin ödül kazandığı seneleri bul */
Athlete,Sport,
lead(Year,0) over(partition by Athlete,Sport order by Athlete) as first,
lead(Year,1) over(partition by Athlete,Sport order by Athlete) as second,
lead(Year,2) over(partition by Athlete,Sport order by Athlete) as third,
FROM sample.summer_medals
Where Year >=1980))

Where sol IS NOT NULL

/* ÇOK GÜZEL ÇÖZDÜM AMA Bİ SPORCU AYNI YILDA 2 KERE ÖDÜL KAZANMIŞSA (Ex: 04,08,08,12) HİÇBİR SIRALI 3 LÜ BİRBİRİNİN ARDIŞIK 4 TOPLAMI ETMİYOR. BU CORNER CASE İ HANDLE EDEMEDİM :'''''''''''( */
```


	
## Soru 3
Bu çalışmada çıkarmak istediğimiz bilgi, günün her bir dakikası için aktif kullanıcı sayısının hesaplanması.

```
SELECT * ,  sum(device) OVER (
                          ORDER BY hview_ts,mview_ts
                              ROWS between 4  PRECEDING and current row) AS sumsums
FROM(
select EXTRACT(minute from view_ts) mview_ts,
EXTRACT(hour from view_ts) hview_ts,
count(distinct deviceid) as device
from sample.pageview 
GROUP BY hview_ts,mview_ts
ORDER BY hview_ts,mview_ts)
```

## Soru 4
## :cry:
