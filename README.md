# ABAP-WEEKEND-AND-SPECIAL-DAY-TRANSACTIONS

*&---------------------------------------------------------------------*
*& Report ZCRM_ODEV_10
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT zcrm_odev_10.
*INCLUDE ICONS.  "icon ekleme
* WRITE ICON_GRAPHICS AS ICON.

PARAMETERS lv_date1 TYPE datum.
PARAMETERS lv_date2 TYPE datum.

DATA : lv_date1_text      TYPE datum,
       lv_date2_text      TYPE datum,
       lv_day_name        TYPE sc_day_txt,
       lv_sonuc           TYPE char10,
       lv_haftasonu_sonuc TYPE int4,
       lv_sayac           TYPE int4,
       lv_a               TYPE int4,
       lt_tarih           TYPE zcrm_tatiller OCCURS 0 WITH HEADER LINE. " zcrm_tatiller tablosundaki bilgiler lt_tarih tablosuna attım.
"-------------------------------------------------------

lv_date1_text = lv_date1. "geçiçi değişken atama.
lv_date2_text = lv_date2.

"Resmi tatil bulma-------------------------"


SELECT * INTO TABLE lt_tarih
  "Sadece kullanıcının girdiği tarihlerin arasındaki tatil günlerini lt_tarih tablosuna attım.
FROM zcrm_tatiller
WHERE t_date => lv_date1_text  AND t_date =< lv_date2_text OR
t_date =< lv_date1_text  AND t_date => lv_date2_text.


lv_sayac = lines( lt_tarih ). "lines komutu tablonun satır sayısını verir.

IF lv_date1 >= lv_date2.
  lv_sonuc =  lv_date1 - lv_date2."Gün farkını hesaplama.

  WHILE lv_date1 >= lv_date2.

    CALL FUNCTION 'DATE_COMPUTE_DAY_ENHANCED'
      EXPORTING
        date    = lv_date1
      IMPORTING
        weekday = lv_day_name.

    IF lv_day_name <> 'SATURDAY' AND
       lv_day_name <> 'SUNDAY' .
      lv_haftasonu_sonuc = lv_haftasonu_sonuc + 1.
    ENDIF.
    lv_date1 = lv_date1 - 1. "gün sayılarını eşitleyene kadar döngü dönsün.

  ENDWHILE.

ELSEIF lv_date1 <= lv_date2."--------------------

  lv_sonuc =  lv_date2 - lv_date1_text.

  WHILE lv_date1 <= lv_date2.
    CALL FUNCTION 'DATE_COMPUTE_DAY_ENHANCED'
      EXPORTING
        date    = lv_date2
      IMPORTING
        weekday = lv_day_name.

    IF lv_day_name <> 'SATURDAY' AND
       lv_day_name <> 'SUNDAY' .

      lv_haftasonu_sonuc = lv_haftasonu_sonuc + 1.

    ENDIF.

    lv_date2 = lv_date2 - 1.

  ENDWHILE.

ENDIF.

lv_sonuc = lv_sonuc + 1."BAŞLANGICI EKLE.
lv_sayac = lv_haftasonu_sonuc - lv_sayac.

WRITE : / lv_date1_text,'İLE ',lv_date2_text,'TARİHLERİ ARASINDA ',lv_sonuc,'GÜN BULUNMAKTADIR. '.
WRITE : / lv_date1_text,'İLE ',lv_date2_text,'TARİHLERİ ARASINDA ',lv_haftasonu_sonuc  ,'HAFTAİÇİ GÜN BULUNMAKTADIR. '.
WRITE : / 'RESMİ TATİLLER HARİÇ',lv_sayac,'GÜN BULUNMAKTADIR.'.
"-----------------------------------
" tarihleri hafta başına kadar küçültüp büyüteceğiz.
