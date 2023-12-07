# Labor 12 - Haladó JavaScript

## Bevezetés

A laborok során a hallgatók laborvezetői segítséggel, majd önállóan végeznek feladatokat a webes technológiák gyakorlati megismerése érdekében.

### Modern JavaScript funkciók és eszközök

A JavaScript nyelv napjainkban rohamosan (talán lassan csökkenő ütemben) fejlődik, de a böngészőgyártók lassan felvették az iramot ECMAScript szabványosítási folyamattal. A manapság támogatandó böngészők legtöbbje szinte az összes ECMAScript 5 és 6 funkciót támogatja. Az Internet Explorer egy elavult, manapság kerülendő böngésző, ami támogat bizonyos ES5 funkciókat, de új funkciófrissítéseket nem kap. Kevés érv maradt a használata mellett, ugyanis a Microsoft Edge böngésző esetében is áttért a Chromium motorra, ami még Windows 7 OS-en is elérhető. Kirívó eset gyakran néhány kevésbé elterjedt mobil böngésző (pl. Opera Mini), valamint a Safari iOS és Mac verziói is hagynak némi kívánni valót maguk után (a különféle JavaScript funkciók támogatása terén). Ezzel azt mondhatjuk, hogy modern JavaScript alapú alkalmazások fejlesztésekor elegendő az ún. "örökzöld" böngészőket támogatnunk, amik naprakészen tartják magukat folyamatos frissítésekkel.

Az új nyelvi funkciók jelentős része megfeleltethető korábban alkalmazott programozási mintáknak, ezáltal az újabb funkciókat (új ECMAScript verziókban megjelenő szabványos elemeket) lefordíthatjuk szabványos korábbi ES verzióra (jellemzően ES5-re). A [**babel**](https://babeljs.io/) fordító a "modern" forrásunkat képes átfordítani erősen kompatibilis JavaScriptté. Említésre méltó még a [TypeScript](https://www.typescriptlang.org/), ami a JavaScript nyelvre épül, kibővítve azt különféle funkciókkal, elsősorban a típusinformációk rendszerével.

!!! info "Typescript"
    A [TypeScript](https://www.typescriptlang.org/) nyelv a fenti fordítási folyamatot annyival egészíti ki, hogy fordítási időben különféle vizsgálatokat végez a kódon, így a hibáink akár már fordítási időben is kiderülhetnek. Elsősorban ehhez típusvizsgálatokat és statikus kódanalízist hajt végre. A VS Code az analízist TypeScript segítségével a normál JavaScript fájlokon is elvégzi, ezért kapunk IntelliSense-t, sőt, ezért jelennek meg esetenként változók, paraméterek típusai is a segítségben.

Fontosabb modern JS képességek:

* **arrow function**: `function (param) { return param + 1; }` helyett írhatjuk a rövidebb `param => param + 1;` kódot. Ezen felül az arrow function nem rendel külön értéket a `this` változónak, így a this ilyen esetekben a **külső** függvényre mutat (a függvényen belül ugyanaz a `this`, mint a hívó fél számára).
* **string interpolation**: a string interpoláció sablonozást, "template-ezést" jelent, a string-ben különböző helyőrzőket helyezhetünk el, amelyek kiértékelődnek: 

    ```js 
    `Hello, I'm ${this.getName()}!`
    ```

* **const** és **let**: a `var` "univerzális" változódeklaráció helyett érdemesebb használni a `let` és `const` kulcsszavakat: előbbi változtathat értéket, utóbbi pedig nem. Előnyük, hogy valóban blokkszintűek, a `var` képes blokkok között is érvényesülni (sajnos).
* **class**: használhatjuk az objektum-orientált class kulcsszót, amelyet korábban címkézetlen, közönséges konstruktorfüggvényekkel próbáltunk körülírni:

    ```js
    class Dog {
        constructor(name) {
            this.getName = () => name;
        }
        bark() {
            alert(`Hello, I'm ${this.getName()}!`);
        }
    }

    const spot = new Dog("Spot");
    spot.bark();
    ```

* **import**/**export**: fájljainkban változókat, osztályokat deklarálhatunk, melyeket kívülről használhatnak (kvázi, mint a publikus láthatóság), ezeket az **export** kulcsszóval látjuk el. A másik oldalon, ahol használni szeretnénk a fájlból egy publikált szimbólumot, az **import** kulcsszóval tehetjük ezt meg, a fájl elérési útját megadva:

    ```js title="dog.js"
    export class Dog { 
        bark() => console.log(`Woof! I'm ${this.name}!`)
    }
    ```

    ```js title="barks.js"
    import { Dog } from './dog';

    export function makeNewDogAndBark(name) {
        const dog = new Dog(name);
        dog.bark();
        return dog;
    }
    ```

!!! info "Babel nélkül"
    Az itt bemutatott JavaScript funkciók mindegyike elérhető már a böngészőben, így használhatjuk őket babel és webpack nélkül is, de erre az útmutató részletes utasítást nem ad. Ha valaki webpack nélkül szeretné elkészíteni a labort, úgy:

    * a `ClientApp` helyett közvetlenül a `wwwroot`-ba dolgozzon,
    * az `import { X } from 'x'` helyett mindenütt `import { X } from 'x.js'` szintaxist használjon,
    * Bootstrap belinkelése történhet közvetlenül CDN-ről: `<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">`,
    * a kiinduló JavaScript fájl a belinkelt `guessgame.js` lesz a `client-start.js` helyett (az utóbbi fájlra nem lesz szükség),
    * a `guessgame.js` hivatkozásakor arra `module`-ként kell hivatkozni (különben az `import` utasítások nem fognak működni), tehát a kiinduló `<script>` elem lecserélendő: `<script src="guessgame.js" type="module">`.
 
    A működés ebben az esetben alapvetően más: a webpack segítségével az összes .js fájlunkból egyetlen, összecsomagolt fájl készülne, a közvetlen modulbetöltéssel viszont minden hivatkozott .js fájl külön-külön HTTP kérésekkel jut el a böngészőbe. Ez fejlesztési időben nem gond, de éles alkalmazásnál mindenképpen valamiféle build folyamat, SPA CLI eszköz vagy modulcsomagoló használata javasolt.

## Feladat 1 - Előkészítés

A laboron készítendő alkalmazás egy **kisebb/nagyobb barkóba** lesz. A "gép" gondol egy számra, majd a felhasználó dolga kitalálni a számot. A tippre a válasz mindig "kisebb", "nagyobb" vagy "talált".

A feladatok megoldása során ne felejtsd el követni a feladat beadás folyamatát [Github](../../tudnivalok/github/GitHub.md).

1. Moodle-ben keresd meg a laborhoz tartozó meghívó URL-jét és annak segítségével hozd létre a saját repository-dat.
2. Várd meg, míg elkészül a repository, majd checkout-old ki.
    * Egyetemi laborokban, ha a checkout során nem kér a rendszer felhasználónevet és jelszót, és nem sikerül a checkout, akkor valószínűleg a gépen korábban megjegyzett felhasználónévvel próbálkozott a rendszer. Először töröld ki a mentett belépési adatokat (lásd [itt](../../tudnivalok/github/GitHub-credentials.md)), és próbáld újra.
3. Hozz létre egy új ágat `megoldas` néven, és ezen az ágon dolgozz.
4. A neptun.txt fájlba írd bele a Neptun kódodat. A fájlban semmi más ne szerepeljen, csak egyetlen sorban a Neptun kód 6 karaktere.
5. Nyissuk meg a Visual Studio Code-dal a repository `feladat\ClientApp` mappáját (File -> Open Folder)!
6. Az alkalmazás futtatásához adjuk ki a Terminalban (Ctrl+ö) az alábbi parancsokat **Figyelem! Előző laborokhoz képest változás!**

    ```cmd
    npm install
    npm run start
    ```

    !!! warning "NPM cache"
        Fontos! A laborgépeken nem vagy nem mindig érhető el megfelelően az NPM lokális cache példánya, ezért használjuk helyette itt az `npm install --cache .cache` parancsot, ami az aktuális mappában egy `.cache` nevű mappát használ a központi gyorsítótár helyett. Lokális gépen is használhatjuk ezt a parancsot, de ott elegendő (kell, hogy legyen) az `npm install` is.

        **Ez a `.cache` mappa NE KERÜLJÖN commitolásra!**

7. Próbáljuk ki az alkalmazást böngészőben!

!!! example "BEADANDÓ (1 pont)"
    Másoljon be egy képernyőképet az alkalmazás kezdőképernyőjéről! (`f1.png`)

### Kiinduló bemutatása

#### NPM

A jelenlegi kliens alkalmazásunk egy **node.js** és **npm** alapú fejlesztési eszközkészletet használ, amely jelenleg a legelterjedtebb JS alapú munkafolyamat az iparban is.
Ennek az alap konfigurációja (amelyet akár projektfájlnak is tekinthetünk) a `package.json` fájlban található, amelynek a legfontosabb elemei:

* **Projekt metaadatok:** tartalmazzák a projekt tulajdonságait (név, szerző, verzió, stb.)
* **Függőségek:** kliensoldali JavaScript függőségeket (`dependencies`) és fejlesztés idejű eszközöket (`devDependencies`) definiálhatunk az NPM csomagkezelőből, amelyeket az `npm install` parancs tölt le a `node_modules` mappába. Ebben a projektben kliens oldalon használjuk a Bootstrapet és a jQueryt, fejlesztés időben pedig a Webpacket és Babelt.
* **Parancsok**: a `scripts` tulajdonságban előre definiált parancsokat vehetünk fel, amelyeket az npm-en keresztül tudunk futtatni `npm run <parancs neve>` formában. Esetünkben a `start` az érdekes, mivel az lefuttatja a webpack fordítási folyamatát, majd nyit egy fejlesztés idejű szervert adott porton (mint előző órákon a `http-server`) `serve --port XXXX`, és megnyitja a böngészőt is `--open`.

!!! warning "Böngésző frissítés"
    Az alkalmazás indulását követően (`serve` módban), ha új JS fájlokat hozunk létre és hivatkozzuk őket, a Webpack HMR (Hot Module Replacement) ezeket a fájlokat automatikusan frissíti a böngészőben. Ettől függetlenül előfordulhat, hogy a kliens oldalt újra kell indítanunk, ekkor továbbra is szükséges a böngészőben frissítenünk F5-tel.

#### Webpack, babel

A [Webpack](https://webpack.js.org/) egy "modulcsomagoló". A JavaScript fájljainkat érdemes külön tartani, hogy ne többtízezer soros kódfájljaink legyenek, hanem minden a saját helyén legyen - mivel a böngészőben sok fájlt letölteni pedig még HTTP/2-vel sem optimálisabb, mintha egy nagy fájlt töltenénk le. Webpack segítségével többek között a JS fájljainkat minifikálni tudjuk, össze tudjuk őket csomagolni kevesebb fájllá, valamint különféle plusz funkciókat tudunk pluginekkel és betöltőkkel az alkalmazásunk terjesztési folyamatába ékelni, pl. source map fájlokat, transpilereket vagy kép-optimalizálókat használni. Manapság gyakorta használt funkciója a *Hot Module Replacement (HMR)*, amely bármiféle újraindítás nélkül, amikor a forrásfájlunk módosul, értesíti a böngészőt a változásról és azonnal az új kód töltődik be (frissíteni sem szükséges a böngészőt). Gyakran a Webpacket valamilyen magasabb szintű keretrendszer részeként (pl. Angular) használjuk, előlünk el van fedve, de használhatjuk kézzel is. Hátránya, hogy a konfiguráció gyakran nagyon bonyolult, a dokumentációja pedig nem a legjobb minőségű.

A webpack konfigurációja a `webpack.config.js` fájlban található, amelyben az alábbiak kerülnek konfigurálásra:

* app belépési pontja: a kliens oldali kód a ClientApp mappában lesz található. **Az alkalmazás belépési pontja a `ClientApp/src/client-start.js` fájl.**
* babel fordító a js fájlokra
* `HtmlWebpackPlugin` annak érdekében, hogy az index.html sablonozható legyen, így a függőségek behivatkozását nem nekünk kell megtenni, mert ezt a webpack automatikusan injektálja. A fordítás után ez a guessgame.js névre hallgat, ez az összecsomagolt, kliens oldali JavaScript alkalmazásunk.
* jQuery plugin

## Feladat 2 - Modern JavaScript funkciók

A megoldás során használjuk az objektumorientált megközelítést és a modern JS funkciókat! Igyekezzünk komponens-orientáltan gondolkodni: **egy objektum komponens, ha megjelenik a felületen a reprezentációja**, képes kommunikálni más objektumokkal és komponensekkel, ezen felül lehet állapota (mezői, tulajdonságai, amiket karbantart).

Az alkalmazásunknak szüksége lesz egy "gépre", ami majd kigondolja a számot. Az egyszerűség kedvéért most ez egy 1 és 100 közötti szám lesz, az érték nem konfigurálható. Szimuláljuk, hogy a "számítás" komplex, úgyhogy kis késleltetést viszünk majd abba, amíg a választ visszakapjuk a tippünkre.

Az objektumaink, melyek a felületen is megjelennek, rendelkezni fognak egy `render()` függvénnyel, és lesz egy (az alkalmazás szempontjából) globális `render()` függvényünk is, ami minden komponenst kirajzol azok `render()` függvényének meghívásával.

Először készítsük el az időzítőt! Az időzítő a játék indulásakor elkezdi számolni **a kliensen** az eltelt időt. Más elemekről nem tud, önállóan működni képes, elindulni és megállni tud, el lehet tőle kérni formázottan az eltelt időt, és ki tudja rajzolni az eltelt időt a felületre. A komponens kódja így az alábbi:

```js title="ClientApp\src\timer.js"
export class Timer {
    start() {
        this.started = Date.now();
        this.interval = setInterval(() => this.render(), 150);
    }

    stop() {
        this.stopped = Date.now();
        clearInterval(this.interval);
    }

    clear() {
        this.started = this.stopped = undefined;
    }

    getElapsedTime() {
        return this.started ? Math.floor(((this.stopped ? this.stopped : Date.now()) - this.started) / 1000) : "-";
    }

    render() {
        document.getElementById("timer").innerText = this.getElapsedTime();
    }
}
```

Az időzítőt önmagában tesztelhetjük, ehhez példányosítjuk, elindítjuk, majd leállítjuk:

```js title="ClientApp\src\client-start.js"
import { Timer } from './timer';

window.addEventListener('DOMContentLoaded', () => {
    const timer = new Timer();
    timer.start();
    setTimeout(() => timer.stop(), 3000);
});
```

Az időzítő persze önmagában nem egy hasznos funkció a játékban, viszont maga a játék, mint komponens egy eleme. A játékos is ilyen elem, róla csak a nevét kell tudnunk, viszont ez idővel fog csak ismertté válni számunkra, tehát egy Promise objektummal tudjuk reprezentálni.

``` JS title="ClientApp\src\player.js"
export class Player {
    constructor() {
        this.onNameSet = new Promise((resolve, reject) => {
            document.getElementById('start-form').addEventListener('submit', e => {
                e.preventDefault();
                const name = document.getElementById('name-input').value;
                if (name && name.length) {
                    resolve(name);
                }
            });
        });

        this.onNameSet.then(name => {
            this.name = name;
            this.render();
        });
    }

    render() {
        for (let element of [document.getElementById('name-input'), document.getElementById('start-button')])
            element.disabled = !!(this.name && this.name.length);
    }
}
```

A Promise beteljesedik, amikor a `#start-form` űrlapot elküldik, és a `#name-input` értéke nem üres. Erre a Promise-re a játékos maga is feliratkozik, és sikeres beteljesedés esetén a `name` property-t beállítja magának. A `render()`-ben megvizsgálja, hogy van-e név, és ha van, a `disabled` attribútumokat megfelelően beállítja.

!!! note await
    Használhatnánk a Promise bevárására az `await` kulcsszót is; ez viszont konstruktorban nem használható.

Láthatjuk, hogy mindenki saját magát rendereli ki, amikor állapotváltozást észlel.

Az egész összekötésére hozzuk létre a Game-et, ami magát a játékot reprezentálja. A Game már biztonsan ismer egy időzítőt és egy játékost. A játékos nevének változására iratkozzunk fel, ez fogja elindítani a játékot magát. A kezdeti állapotot is ki tudja renderelni. Egy `components` property-ben fogjuk gyűjteni az egyes komponenseinket, és azoknak meg tudjuk hívni mindnek a `render` függvényét (amennyiben a komponensnek van `render` függvénye):

```js title="ClientApp\src\game.js"
import { Player } from './player';
import { Timer } from './timer';

export class Game {
    constructor() {
        this.timer = new Timer();
        this.player = new Player();

        this.player.onNameSet.then(() => this.start());

        this.components = [this.timer, this.player];

        this.render();
    }

    render() {
        this.components.map(c => c && typeof(c.render) === 'function' && c.render());
    }

    start() {
        this.timer.start();
    }

    onGuessed(num, guess) {
    }

    onGuessing(num) {
    }
}
```

A játékot a dokumentum betöltésekor példányosítjuk. Az onGuessed függvényt kívülről tudják majd meghívni, jelezvén, hogy egy tipp kisebb vagy nagyobb volt a "gondolt" számnál, esetleg talált. Az onGuessing függvénnyel egy tippet tudnak majd a játéknak küldeni. A `client-start.js` tartalmát **cseréljük le** az alábbira:

```js title="ClientApp\src\client-start.js"
import { Game } from './game';

window.addEventListener('DOMContentLoaded', () => {
    const game = new Game();
});
```

!!! example "BEADANDÓ (1 pont)"
    Másoljon be egy képernyőképet, ahol megadja a nevét, és ennek hatására az időzítő elindult, a név bekérésére szolgáló input, valamint az indításra szolgáló gomb pedig le vannak tiltva. (`f2.png`)

## Feladat 3 - Játéklogika

A játéklogikát egy random generátor által adott 1 és 100 közötti egész szám, és a tippelésre adott kisebb/nagyobb válasz interfésze adja. Írjuk meg a játéklogikát a kliens oldalon!

```js title="ClientApp\src\logic.js"
export class Logic {
    constructor(game) { this.game = game; }

    startGame() {
        const secretNumber = Math.floor(((Math.random() * 100) + 1));
        this.secretGuess = (num) => secretNumber < num ? 'less' : secretNumber > num ? 'greater' : 'correct';
    }

    guess(num) {
        if (this.secretGuess && typeof (this.secretGuess) === 'function') {
            setTimeout(() => this.game.onGuessed(num, this.secretGuess(num)), 400);
        }
    }
}
```

A titkos számot a lokálisan, a `startGame` függvényben tárolt `secretNumber` változóban tároljuk. Ez a változó kívülről nem látható, az egyetlen függvény, aki ismeri ezt az értéket, a `secretGuess`. A `guess` "publikus" függvényt meghívva a logika értesíti a játékot a tipp helyességéről az `onGuessed` függvényen keresztül. A válasz késleltetve érkezik, a késleltetés 400ms.

A játéknak szüksége lesz valamilyen mechanizmusra, hogy meg tudja jeleníteni a helyes/helytelen tippeket. Ezt egy Guesses osztály példányában fogjuk tárolni, ami a felületen megjelenő, tippeket tartalmazó táblázatot fogja frissíteni szükség esetén.

```js title="ClientApp\src\guesses.js"
export class Guesses {
    constructor() {
        this.clear();
    }

    addGuess(num, value) {
        if (num && value) {
            this.guesses.push({ num, value });
            this.render();
        }
    }

    clear() {
        this.guesses = [];
        this.render();
    }

    render() {
        for (let tr of Array.from(document.querySelectorAll('#guesses tbody tr')))
            tr.remove();

        for (let tr of (this.guesses.map((g, i) => 
                `<tr>
                    <td>${i + 1}</td>
                    <td class='text-right'>${g.num}</td>
                    <td class='bg-${g.value === 'correct' ? 'success' : 'danger'}'>${g.value === 'correct' ? '!!!' : g.value === 'less' ? '&gt;' : '&lt;'}</td>
                </tr>`).reverse()))
            document.querySelector('#guesses tbody').innerHTML += tr;
    }
}
```

Már csak a felületről érkező tippet kell kezelnünk, ehhez egy Guess osztályt hozunk létre:

```js title="ClientApp\src\guess.js"
export class Guess {
    constructor(game) {
        document.getElementById('guess-form').addEventListener('submit', e => {
            e.preventDefault();
            const value = parseInt(document.getElementById('guess-input').value);
            if (!isNaN(value) && value > 0 && value <= 100)
                game.onGuessing(value);
            document.getElementById('guess-form').reset();
        });
        document.getElementById('guess-form').reset();
        this.setEnabled(false);
    }

    setEnabled(value) {
        this.enabled = !!value;
        this.render();
    }

    render() {
        for (let element of [document.getElementById('guess-input'), document.getElementById('guess-button')]) {
            element.disabled = !this.enabled;
        }

        if (this.enabled) {
            document.getElementById('guess-input').focus();
        }
    }
}
```

Össze kell kötnünk a játékot a logikával, ill. a korábbi tippek tárolásáért felelős objektummal, valamint kezelnünk kell a felületről, a felhasználó részéről érkező tippet. Frissítsük a `game.js` tartalmát az alábbira:

```js title="ClientApp\src\game.js"
import { Player } from './player';
import { Timer } from './timer';
import { Logic } from './logic';
import { Guesses } from './guesses';
import { Guess } from './guess';

export class Game {
    constructor() {
        this.timer = new Timer();
        this.player = new Player();
        this.logic = new Logic(this);
        this.guesses = new Guesses();
        this.guess = new Guess(this);

        this.player.onNameSet.then(() => this.start());

        this.components = [this.timer, this.player, this.logic, this.guesses, this.guess];

        this.render();
    }

    render() {
        this.components.map(c => c && typeof (c.render) === 'function' && c.render());
    }

    start() {
        this.timer.start();
        this.guess.setEnabled(true);
        this.logic.startGame();
    }

    onGuessed(num, guess) {
        this.guesses.addGuess(num, guess);
        if (guess === 'correct') {
            this.guess.setEnabled(false);
            this.timer.stop();
        }
    }

    onGuessing(num) {
        this.logic.guess(num);
    }
}
```

!!! example "BEADANDÓ (1 pont)"
    Másoljon be egy képernyőképet egy játék végeredményéről, ahol láthatók a leadott tippek és az eltelt idő! (`f3.png`)

## Feladat 4 (Önálló) - Toplista

A toplistán kizárólag a legjobb 10 eredménnyel rendelkező egyén jelenik meg. A toplista először a tippek száma, majd a megtett idő alapján csökkenő sorrend szerint rendezett.

??? done "Megoldás"
    A toplista perzisztens, tehát a játék indulásakor a toplista `localStorage`-ből betöltődik, az új bejegyzések pedig ide perzisztálódnak. Ehhez a `localStorage` globális objektum `getItem(key)` és `setItem(key, value)` metódusai használhatók, a rendezéshez a tömb `sort(callback)` függvénye használható, a callback két elemet kap, amelyekhez egy összehasonlítást kell megadnunk, ami egy számértékkel tér vissza (a kisebb számértékű elem előrébb lesz a listában).

    ```js title="ClientApp\src\toplist.js"
    export class Toplist {
        constructor() {
            this.items = JSON.parse(localStorage.getItem('toplist') || '[]');
            this.render();
        }

        setItem(name, guesses, time) {
            this.items.push({ name, guesses, time });
            this.items = this.items.sort((a, b) => a.guesses + a.time / 1000 - (b.guesses + b.time / 1000)).slice(0, 9);
            localStorage.setItem('toplist', JSON.stringify(this.items));
            this.render();
        }

        render() {
            document.querySelector('#toplist tbody').innerHTML = this.items.map((e, i) => (
                `<tr>
                    <th>${i + 1}</th>
                    <td>${e.name}</td>
                    <td>${e.guesses}</td>
                    <td>${e.time}</td>
                </tr>`
            )).join('');
        }
    }
    ```

    A toplistát a játékhoz kell kötnünk, és a `setItem` metódust meghívni, amikor a játéknak vége.

    ```js title="ClientApp\src\game.js"
    /* ... */
    import { Toplist } from './toplist';

    export class Game {
        constructor() {
            /* ... */
            this.toplist = new Toplist();
            /* ... */
            this.components = [this.timer, this.player, this.logic, this.guesses, this.guess, this.toplist];
            /* ... */
        }

        /* ... */

        onGuessed(num, guess) {
            /* ... */
            if (guess === 'correct') {
                /* ... */
                this.toplist.setItem(this.player.name, this.guesses.guesses.length, this.timer.getElapsedTime());
            }
        }

        /* ... */
    }
    ```

!!! example "BEADANDÓ (1 pont)"
    Másoljon be egy képernyőképet, amelyen legalább 3 különböző eredmény látható! (`f4.png`)

## Feladat 5 (Önálló) - Újrakezdés

A fenti gondolatmenetekhez hasonlóan készítse el a játék újrakezdését implementáló logikát! Az újrakezdés az alábbiakat jelenti:

* a játék befejezését követően megjelenik a felületen egy *Restart* címkéjű gomb, melyet megnyomva új játék indul (ezt követően ismét eltűnik),
* a felhasználó nevét bekérő űrlap újból engedélyezve lesz, a fókusz ide helyeződik, a felhasználónak lehetősége van átírni a nevét és új játékot indítani,
* a bal oldali táblázatban látható korábbi tippek ürülnek.
* a timer által jelzett érték eltűnik.

!!! example "BEADANDÓ (1 pont)"
    Illesszen be képernyőképet a felületen dinamikusan elhelyezett gombról! (`f5.png`)

## Feladat 6 iMSc - Szerver oldali logika (2 iMSc pont)

A kliens oldalon tárolt logikát helyezze át a szerver oldalra a `Backend` mappában lévő `GuessWS.sln` ASP.NET Core 6-os projektbe!

!!! important "Projekt indítása"
    Az iMSc feladathoz telepítve kell lennie a [.NET **SDK**](https://dotnet.microsoft.com/download/dotnet/6.0) legalább 6.0-s (de még nem 7-es) verziójának is a gépre (ez Visual Studio 2022-vel is települ).

    A projekt elindításához elegendő a megszokott Visual Studio-s _Debug_ vagy _Start without debugging_ funkciót választani. Ilyenkor a backend alkalmazásunk elindul viszont szüksége lenne a kliens részekre is. Ezt úgy oldjuk meg jelenleg, hogy a kliens alkalmazás futtatására továbbra is szükségünk lesz (pl VS Code-ban `npm start`), és a backend a statikus fájlok (html, js, css) kiszolgálására átproxyza a kéréseket a webpack szerver felé. Ez a `Program` osztályban került definiálásra:

    ```cs
    app.UseSpa(spa =>
    {
        spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    });
    ```

    Tehát ha backenddel tesztelnénk az alkalmazást az alábbi módon kell eljárjunk:

    1. indítsuk el a frontendet az előző feladatok mintájára
    2. indítsuk el a backendet
    3. teszteljük az alkalmazást a backend portján

A szerveren fut egy WebSocket kiszolgáló, mely bármilyen `wss://localhost:port`-on futó kérésre válaszol (HTTP esetén (`ws://localhost:port`)).

A szerverkapcsolatot az alábbi osztály reprezentálja (a portszámot szükséges lehet átírnia a saját szerver portszámára):

```js title="ClientApp\src\socketserver.js"
export class SocketServer {
    constructor() {
        this.socket = new WebSocket('wss://localhost:7579/ws');
        this.open = false;
        this.socket.onopen = () => {
            this.open = true;
            this.socket.onmessage = e => {
                this.onRecieve(JSON.parse(e.data));
            }
        };
    }

    send(action, name, guess) {
        if (this.open) {
            const json = JSON.stringify({ action, name, guess });
            console.log(`Sending message: ${json}`)
            this.socket.send(json);
        }
    }

    onRecieve(data) {
        console.log(data);
    }
}
```

!!! warning URL
    A socket szerver URL-t szükséges lehet átírni: a portszámnak a szerver portszámának kell lennie (az elindított backend konzolja kiírja, hol indul a szerver), és `http` esetén `ws`, `https` esetén pedig `wss` protokollt kell (/érdemes) használni.

A szervert az alábbi kód mintájára tudja használni:

```js
const server = new SocketServer();

setTimeout(() => {
    server.send("setName", "John Doe");

    server.send("startGame");

    let guess = 0;

    setInterval(() => server.send("guess", undefined, ++guess), 500);
}, 2000);
```

A szerver az alábbi kérésekre figyel:

* `startGame`: játék indítása.
* `setName`: név beállítása az aktuális játékoshoz. Szükséges a "name" paraméter megadása is.
* `guess`: tipp küldése. Szükséges a guess paraméter megadása is.
* `toplist`: toplista lekérése.

A szerver az alábbiakat küldi:

* a toplistát az alábbi JSON-formátumban küldi a szerver: `[{ name, guesses, timeInSeconds }, ...]`,
* egy tippre a szerver az összes klienst értesíti, az alábbi objektumot küldve: `{ name, guess, value, timeInSeconds }`.

Valósítsa meg az alábbiakat:

* a szerver tárolja a játék állapotot, nem a kliens,
* a szerver értesülésére a kliensek fel vannak iratkozva, és megosztják az állapotot (több böngésző ablakkal tudja demonstrálni),
* a szerver tárolja a toplistát,
* a szerver küld válaszeseményeket a tippelésre, minden feliratkozót értesít.

!!! example "BEADANDÓ (2 iMSc pont)"
    Készítsen képernyőképet a működő funkcióról, ahol látszik a fejlesztői eszköztár Network füle is egy releváns websocket kommunikációval! (`f6.imsc.png`)
