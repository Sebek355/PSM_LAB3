Sprawozdanie: Internacjonalizacja Aplikacji (i18n)

W niniejszym raporcie przedstawiono wyniki implementacji internacjonalizacji, obsługi liczby mnogiej (plurals) oraz testów interfejsu przy użyciu pseudojęzyka w aplikacji "Losowy Żart".

1. Porównanie wersji językowej (Polski vs Angielski)

Poniższe zrzuty ekranu przedstawiają główny widok aplikacji po zastosowaniu mechanizmu internacjonalizacji. Zmiana języka następuje płynnie dzięki wykorzystaniu AppCompatDelegate.

2. Obsługa liczby mnogiej (Plurals)

Zaimplementowano mechanizm Plurals, który dynamicznie dopasowuje końcówki wyrazów do aktualnej wartości licznika. Poniżej widoczne jest poprawne formatowanie dla różnych przypadków w języku polskim:

3. Testowanie pseudojęzykiem (Pseudo-locales)

Aby zweryfikować elastyczność interfejsu (UI) na dłuższe ciągi znaków oraz upewnić się, że żaden tekst nie został zahardkodowany ("na sztywno" w kodzie), uruchomiono aplikację w trybie pseudojęzyka (en-XA).

Lista wniosków i potrzebnych poprawek po teście w pseudojęzyku:

Sukces ekstrakcji zasobów: Wszystkie ciągi tekstowe widoczne na ekranie posiadają nawiasy kwadratowe [...]. Oznacza to, że 100% tekstów zostało poprawnie przeniesionych do plików strings.xml i nie ma w kodzie zahardkodowanych stringów.

Kwestia elastyczności UI (Poprawka): Pseudojęzyk sztucznie wydłuża teksty (dodając znaki i słowa takie jak one two three), co symuluje języki o dłuższych słowach (np. niemiecki). Widać, że tekst w okrągłym przycisku ledwo mieści się w wyznaczonych mu ramach. Jeśli przycisk posiada "sztywny" rozmiar (np. modifier.size(160.dp)), w innych językach tekst może zostać ucięty. Rozwiązanie: Należy zamienić sztywne wartości na elastyczne (np. używając wewnętrznego paddingu i pozwalając przyciskowi skalować się do zawartości - wrapContentSize).

4. Dlaczego warunek sprawdzający liczbę pojedynczą jest w języku polskim niewystarczający?

W języku angielskim obsługa liczby mnogiej jest prosta, ponieważ istnieją w nim zasadniczo dwa stany:

liczba pojedyncza (1 item)

liczba mnoga (0 items, 2 items, 3 items...).

Gdybyśmy przenieśli tę logikę (sprawdzanie tylko warunku count == 1) na język polski, otrzymalibyśmy błędy gramatyczne. W języku polskim odmiana liczebników zależy od cyfry jedności oraz dziesiątek, wymuszając użycie aż trzech form mnogich:

one (1): 1 żart

few (2-4, 22-24, ...): 2 żarty, 3 żarty, 4 żarty

many (5-21, 25-31, ... oraz 0): 5 żartów, 13 żartów, 0 żartów

Dlatego użycie struktury <plurals> wbudowanej w Androida, która posiada dedykowane tagi dla one, few oraz many, jest absolutnie konieczne do poprawnego wyświetlania gramatyki w języku polskim.

5. Który zestaw zasobów powinien być domyślny i dlaczego?

Domyślnym zestawem zasobów (umieszczonym w głównym folderze res/values/strings.xml, bez przyrostka językowego) powinien być język angielski.

Dlaczego?
Folder domyślny pełni funkcję ostatecznego zabezpieczenia (tzw. "fallback"). Jeśli użytkownik pobierze naszą aplikację, a jego telefon jest ustawiony na język, którego nie wspieramy (np. hiszpański lub japoński), system Android załaduje zasoby właśnie z domyślnego folderu values.
Ponieważ język angielski jest powszechnie uznawany za globalny standard komunikacji (język lingua franca), istnieje znacznie większa szansa, że obcokrajowiec zrozumie interfejs po angielsku niż po polsku. Polskie tłumaczenie powinno być celowo umieszczone w dedykowanym folderze res/values-pl/.
