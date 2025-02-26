---
share: "true"
url: baselinker-pa-mixer
---
# Baselinker pa_mix & WooCommerce integration

Niedawno miałem styczność z ustawieniami Baselinkera pa_mixer (definicje pól opisowych wychodzących z BL) w integracji WooCommerce. Wiem, że ta informacja może być cenna, ponieważ obecna dokumentacja Baselinkera nie zawiera żadnych informacji o pa_mixer.

Potrzebowałem rozwiązać następujący problem:  
- Klient Baselinkera ma utworzone i już wypełnione dwie grupy cenowe (hurtową i detaliczną).  
- Potrzebowaliśmy pobierać cenę detaliczną do standardowego pola ceny WooCommerce.  
- W tym samym czasie musimy również pobierać cenę hurtową do meta pola produktu WooCommerce `_justb2b_price` (nasza wtyczka do zaawansowanej obsługi klientów B2B w sklepach internetowych WooCommerce).  
- Niestety, przy aktualizacji produktu w WooCommerce (integracja Baselinker + WooCommerce) musimy wybrać jedną cenę, którą przesyłamy z BL do Woo. Nawet gdybyśmy próbowali przesłać obie ceny kolejno, efekt nie byłby zadowalający – cena po prostu się nadpisze.

Po tygodniu wymiany maili z supportem Baselinkera znalazłem następujące rozwiązanie:  
- Tworzymy dwie różne integracje z WooCommerce, które nazwałem „woo detal” oraz „woo hurt” – można użyć tych samych kluczy API WooCommerce.  
- „Woo detal” ustawiamy standardowo – można również ustawić automatyczną synchronizację stanów magazynowych i cen co godzinę.  
- „Woo hurt” jest bardziej skomplikowane – musimy włączyć jedynie synchronizację cen co godzinę i nie synchronizować stanów, ponieważ to już odbywa się w „woo detal”.  
- Następnie musimy wykonać kluczowe działanie w integracji → woo hurt → ustawienia → zaawansowane → pa_mixer, wklejając następującą wartość:
  `true_price = "{price}", price = "{null}", {meta_data._justb2b_price} = "{true_price}"`  
  Czyli nie wysyłamy standardowej ceny, lecz przesyłamy tę cenę do niestandardowego pola produktu `_justb2b_price`.

Chciałbym również przedstawić swoje wnioski dotyczące możliwości pa_mixer w Baselinkerze, ponieważ, jak już wspomniałem, dokumentacja Baselinkera nie udostępnia szczegółowych informacji na ten temat:  
- Możemy tworzyć zmienne – w naszym przypadku jest to `true_price`.  
- Możemy korzystać z tych zmiennych, np. `{price}`.  
- Możemy uzyskać dostęp do pól danych wychodzących, np. `{meta_data._justb2b_price}`.  
- Możemy tworzyć konkatenacje w cudzysłowie, np. `"{true_price}_{other_var}"`.  
- Możemy nie wysyłać pewnych zmiennych, zmieniając ich wartość na `"{null}"`.

Jestem pewny, że istnieją jeszcze inne możliwości, ale na razie nie miałem potrzeby korzystania z dodatkowych opcji pa_mixer.

Support odpowiadał następująco:

> Dzień dobry,  
> Niestety nie mamy dokumentacji dotyczącej opcji zaawansowanych integracji. Założenie jest takie, że opcje te są wykorzystywane w specyficznych przypadkach użycia integracji i zazwyczaj są konfigurowane przez support Baselinkera.  
> Jeżeli będą mieli Państwo konkretne pytania dotyczące użycia opcji pa_mixer i innych, chętnie Państwu odpowiem.  
> Pozdrawiam!

## Wnioski

- **Podział integracji:** Rozwiązanie oparte na dwóch integracjach („woo detal” i „woo hurt”) pozwala na niezależne przetwarzanie ceny detalicznej oraz hurtowej, co eliminuje problem nadpisywania danych.
- **Elastyczność pa_mixer:** Dzięki pa_mixer możemy definiować niestandardowe zmienne (np. `true_price`), stosować konkatenację oraz kontrolować, które dane są przesyłane do WooCommerce.
- **Brak oficjalnej dokumentacji:** Mimo braku publicznej dokumentacji opcji zaawansowanych, wsparcie Baselinkera jest pomocne w konfigurowaniu niestandardowych ustawień.
- **Optymalizacja przetwarzania:** Umożliwienie przesyłania ceny hurtowej do pola `_justb2b_price` bez nadpisywania ceny detalicznej poprawia spójność danych i efektywność synchronizacji między systemami.


[[baselinker|baselinker]]
[[justb2b|justb2b]]
[[baselinker integration|baselinker integration]]


