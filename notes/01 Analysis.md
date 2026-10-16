Struktura Markusha (Markush structure)

**Dwa zadania, których dotyczy praca**

1. **OCSR** (optyczne rozpoznawanie struktur chemicznych): obrazek jednej konkretnej cząsteczki zamieniany na zapis tekstowy. Autorzy piszą, że ta dziedzina jest już dojrzała.
2. **Parsowanie struktur Markusha:** to samo, ale dla rysunków, gdzie jeden rysunek opisuje całą rodzinę cząsteczek. To zadanie jest dużo trudniejsze, bo trzeba odczytać nie tylko rysunek, ale też tekst z listą dopuszczalnych podstawników (R1 = H, OH itd.) i połączyć jedno z drugim.

Tłumaczenie obrazu na tekst (image-to-text)
Model wizualno-językowy (vision-language model)

OCSR (optical chemical structure recognition) - jedna konkretna cząsteczka - na obrazku jest cząsteczka, w której każdy atom jest znany, np. rysunek kofeiny. Program ma tylko przepisać rysunek na tekst, np. na SMILES (Simplified Molecular Input Line Entry System).

Turning the drawing of one molecule into a text representation a program can parse is known as optical chemical structure recognition (OCSR).

Model musi z obrazka odczytać nie tylko “jakie atomy i jak połączone”, ale też **drobne szczegóły graficzne**: czy klin jest pełny czy kreskowany i w którą stronę jest skierowany. To małe elementy rysunku, łatwe do przeoczenia, a ich pomylenie daje cząsteczkę, która wygląda prawie tak samo, ale jest inna.

Rozpoznanie zwykłej cząsteczki to szczególny przypadek rozpoznania struktury Markusha: Markush bez grup zmiennych, bez adnotacji i bez tekstu podstawników. Dlatego format CXSMILES potrafi zapisać także zwykłą cząsteczkę.

OCSRGlyph to mały, wyspecjalizowany model zbudowany z dwóch części: enkodera Swin-B, który czyta obraz cząsteczki (384 px, wagi wstępnie wytrenowane na ImageNet), oraz 6-warstwowego dekodera trenowanego od zera, który zapisuje SMILES znak po znaku. Całość ma około 94 mln parametrów i generuje wyłącznie goły SMILES, a po dekodowaniu wynik jest czyszczony z halucynowanych fragmentów i kanonizowany w RDKit.

MarkushGlyph jest około 20 razy większy (około 2 mld parametrów) i nie powstał od zera: to gotowy model wizyjno-językowy Qwen3.5-2B-Base, dostrojony metodą LoRA w jednym etapie uczenia nadzorowanego. Dostaje obraz i krótki prompt, a tekst widoczny na rysunku (etykiety R, definicje podstawników) czyta bezpośrednio z obrazu, bez osobnego etapu OCR. Zamiast samego SMILES zwraca pełny rekord `cxsmiles_opt` z grupami R, pozycjami wariantowymi i jednostkami powtarzalnymi.