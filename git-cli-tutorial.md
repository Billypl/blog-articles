# git cli

## Ogólne
### Nazewnictwo commitów:
`PGR-NR_ZGŁOSZENIA_NA_YTRACK(TYP): OPIS COMMITA`  
```
np. PGR-69(UI): dodano menu  
```
(lista typów do ustalenia odgórnie - żeby był porządek i łatwe znajdowanie zmian)  

GH repo styl:
- `SKRÓT_PROJEKTU - NUMER_YOUTRACKA część_nad_którą_pracowano(typ): opis`
- `np. PGR-123 UI(fix): fix bug where user could't open menu`
Typy to:
- fix - poprawki błędów
- feat - (feature) nowa funkcjonalność
- style - poprawa indentacji, struktury projektu, przenosiny plików
- docs - dodanie dokumentacji, aktualizacja readme itp
- refactor - przerobienie klasy / funkcji na bardziej zrozumiałą, mniejszą, rozdzielenie na mniejsze lub przeniesienie do plików
- chore - rzeczy regularne, typu aktualizacja wersji zależności

### Komentarz
Nie mergujemy sami do mastera - kiedy jesteśmy gotowi, PullRequest na gh albo komenda:
```
gh pr create --base master --head BRANCH_DO_SCALENIA --title "TITLE"
```

## Podział gita
Git podzielony jest na 3 obszary. WORKING_DIRECTORY, STAGING_AREA, REPOSITORY  

#### WORKING_DIRECTORY 
to jest folder gdzie stworzyliśmy repozytorium (z folderem .git). Tam są wszystkie pliki, które stworzymy i edytujemy defaultowo. Nie zostaną uploadowane na gita przy commicie.

#### STAGING_AREA 
to przestrzeń, gdzie wędrują pliki po użyciu komendy git add. Te zmiany są zapamiętane, więc jeśli będziemy edytować coś w WORKING_DIRECTORY, to pliki w tym obszarze pozostaną niezmienione, aż do ponownego wywołania git add

#### REPOSITORY 
to przestrzeń z całą historią naszych plików. Kolejna wersja pliku trafia tam po użyciu git commit. Możemy przeglądać tam poszczególne commity, pliki i zmiany w nich robione. Ono nadal jest LOKALNE na danym komputerze

#### ONLINE_REPOSITORY/REMOTE 
(bonus) to instancja repozytorium, najczęściej hostowana na githubie. Pliki trafiają tam z commitów po użyciu komendy git push
Przydatne komendy

## Komendy

### Zainstalowanie gita w konsoli:
```
sudo apt-get install git 
sudo apt-get install gh
gh auth login # tu pojawia się interaktywny program - wybieramy LOGOWANIE PRZEZ PRZEGLĄDARKĘ (ważne!!)
```

Dodanie pliku do STAGING_AREA:  
```
git add NAZWA_PLIKU/FOLDERU
```

Cofnięcie pliku ze STAGING_AREA:  
```
git restore --staged NAZWA_PLIKU/FOLDERU
```

### Local komendy
```
Dodanie commita do REPOSITORY
# defaultowa nazwa
git commit 
# customowa nazwa
git commit -m "WIADOMOŚĆ" 
# zastępuje komendę `git add` (ale plik musi być chociaż raz dodany wcześniej add'em)
git commit -am "WIADOMOŚĆ" 
# edytuje poprzedniego commita (zmienia pliki)
git commit --ammend 
# edytuje poprzedniego commita (zmienia pliki i tytuł)
git commit --ammend -m "NOWA_WIADOMOŚĆ" 
```

### Remote komendy
```
Dodanie commita do ONLINE_REPOSITORY  
git pull # ściągnięcie zmian z repozytorium na githubie  
git push # wysłanie commita do repozytorium na githubie  
git push --force # dodanie pliku mimo konfiktów (nadpisanie remote’a)- UWAGA, USUNIE PRACĘ KOGOŚ INNEGO - NIE ROBIMY TAK  
git push --force-with-leash # nadpisanie remote’a ale tylko wtedy, kiedy ktoś inny nie wprowadził zmian - bezpieczniejsze, nie usunie pracy innych  
np.https://safjan.com/understanding-the-differences-between-git-push-force-and-git-push-force-with-lease/
```

### Branche
```
git branch # wyświetlenie branchy  
git checkout -b NAZWA_BRANCHA # stworzenie brancha (ważny jest branch na którym to odpalacie, bo kopiuje się cały jego stan)  
git push -u origin NAZWA_BRANCHA # wypchnięcie nowej gałęzi na remot’a  
git checkout NAZWA_BRANCHA # zmiana aktywnego brancha  
git checkout - # zmiana aktywnego brancha na ostatnio aktywnego  
git checkout -b NAZWA_BRANCHA_LOKALNEGO NAZWA_BRANCHA_JAKI_CHCEMY_DODAĆ_NA_REMOCIE # przełączenie się na nowego brancha z remot’a, którego jeszcze nie mamy lokalnie
```

### Statusy i historia
```
git status # obecny stan **WORKING_DIRECTORY** i **STAGING_AREA** - na czerwono pliki nie dodane do **STAGING_AREA**, na zielono pliki dodane, ale nie zacommitowane
git log # historia commitów
git log -S SZUKANA_FRAZA -p # historia zmian danej frazy 
git reflog # historia refów - commitów, przepięć, mergów itp
git blame NAZWA_PLIKU # historia edycji każdej linii
git blame -w -C -C -C -L n1,n2 NAZWA_PLIKU # lepsza historia edycji, w liniach od n1 do n2
git diff --word-diff NAZWA_PLIKU # TODO? 
Resetowanie commitów i cofanie stanu
używać tylko gdy pracujemy samemu (druga osoba nie ma żadnych zmian w stage'u / commitów) lub jeśli pracujemy na swoich commitach (tylko te, których jeszcze nie pushowaliśmy)
# dodaje commita, który nadpisuje cofanego commita
git revert COMMIT_HASH  
# --no-commit -> nadpisuje cofanego commita, ale nie dodaje jeszcze nowego commita cofającego, a odkłada go tylko do staging areas (git commit -m MESSAGE żeby stworzyć commita cofającego)
git revert -n COMMIT_HASH 
```
```
# przepina HEADa do danego okresu w czasie - historia dostępna pod komendą `git reflog`
#posiada 3 tryby
git reset COMMIT_HASH 
# przepina HEADa, ale zmiany w STAGE_AREA i WORKING_DIR zostają
git reset --soft COMMIT_HASH 
# (default) przepina HEADa, usuwa ze STAGE_AREA, zostawia WORKING_DIR
git reset --mixed COMMIT_HASH
# przepina HEADa, cofa wszystko - STAGE_AREA i WORKING_DIR 
git reset --hard COMMIT_HASH 
```

## Mergowanie
(tylko w obrębie własnych gałęzi, do mastera wystawiamy PR)

```
# mergowanie brancha na którym jesteśmy z BRANCH_TO_MERGE_NAME
git merge BRANCH_TO_MERGE_NAME 
# mergowanie z wiadomością-commitem o mergu (np. "Merged branch BRANCH_TO_MERGE_NAME to DESTINATION_BRANCH) 
git merge --no-ff BRANCH_TO_MERGE_NAME 

# zaciągnięcie zmian z OTHER_BRANCH_NANE i przepięcie commitu od którego obecny branch był zmieniany na ostatni commit OTHER_BRANCH_NANE 
git rebase OTHER_BRANCH_NANE 

# to samo co wyżej, z tym, że -i (interactive) daje nam możliwość zmiany historii obecnego brancha. Otwiera się plik, w którym każdy commit ma swoją opcję
git rebase -i OTHER_BRANCH_NANE 
# najczęstsze to:
  # pick - po prostu bierze commita normalnie
  # reword - pozwala na zmianę tytułu commita
  # edit - pozwala zmienić zawartość pliku w commicie
  # squash - merguje commita z wcześniejszym, ale pozwala na ustawienie tytułu commita (default to połączenie dwóch nazw)
  # fixup - merguje commita z wcześniejszym i zostawia tylko wcześniejszą nazwę
  # drop - usuwa całkowicie commita z historii
  # break - przerywa interactive rebase'a
git rebase --continue # wznawianie po breaku
git rebase --abort # anulowanie rebase'a 
```

## Aliasy
```
alias na fajny wygląd grafu (wrzućcie go do .bashrc żeby zachowywał się wam między odpaleniami WSLa)
alias lg="git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)'"
```

ta komenda wywołana w ~/bin spowoduje zainstalowanie pakietu z lepszym git diff'em (podświetla tło dokładnie w zmienionych znakach, a nie same całe zmienione linie)
cd ~/bin sudo curl -O https://raw.githubusercontent.com/git/git/fd99e2bda0ca6a361ef03c04d6d7fdc7a9c40b78/contrib/diff-highlight/diff-highlight sudo chmod +x diff-highlight git config --global pager.log 'diff-highlight | less' git config --global pager.show 'diff-highlight | less' git config --global pager.diff 'diff-highlight | less' git config --global interactive.diffFilter diff-highlight
```

created by Billy on 05.11.2024
updated by Billy on 20.05.2025