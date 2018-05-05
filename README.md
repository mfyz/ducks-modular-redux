# Ducks: Redux Reducer Paketleri

<img src="duck.jpg" align="right"/>

Her redux uygulamasi gelistirdigimde, uygulamami parca parca yaziyorim. Bu surecte, kendimi surekli `{actionTypes, actions, reducer}` uclemesini yazarken buluyorum. Bunlari ayri dosyalarda hatta ayri klasorlerde tutuyordum. Ancak 95% oranda sadece tek reducer/action ikilisi bu iliskileri kullanmaya ihtiyac duyuyor.

Bence, bu parcalari tek dosyada toplu sekilde tutmak cok daha mantikli. Boylece izole sekilde, paketlenmis bir sekilde bir arada durabilirler.

## Oneri

### Ornek

Bakiniz: [Common JS Example](CommonJs.md).

```javascript
// widgets.js

// Actions
const LOAD   = 'my-app/widgets/LOAD';
const CREATE = 'my-app/widgets/CREATE';
const UPDATE = 'my-app/widgets/UPDATE';
const REMOVE = 'my-app/widgets/REMOVE';

// Reducer
export default function reducer(state = {}, action = {}) {
  switch (action.type) {
    // reducer ile ilgili seyler buraya gelecek
    default: return state;
  }
}

// Action Creators
export function loadWidgets() {
  return { type: LOAD };
}

export function createWidget(widget) {
  return { type: CREATE, widget };
}

export function updateWidget(widget) {
  return { type: UPDATE, widget };
}

export function removeWidget(widget) {
  return { type: REMOVE, widget };
}

// side effects, only as applicable
// e.g. thunks, epics, etc
export function getWidget () {
  return dispatch => get('/widget').then(widget => dispatch(updateWidget(widget)))
}

```
### Kurallar

Bir modul...

1. KESINLIKLE `reducer()` adinda bir fonksiyonu `export default` ile export etmeli
2. KESINLIKLE aksiyon ureten tanimlari fonksiyon olarak `export` etmeli
3. KESINLIKLE action tiplerini `npm-module-or-app/reducer/ACTION_TYPE` isimlendirme yapisiyla tanimlamali
3. BELKI aksiyon turlerini `UPPER_SNAKE_CASE` seklindeki isimlendirme yapisiyka expor tedebilir, eger disaridan bir reducer dinlemek isterse, tekrardan kullanilabilir sekilde olurlar.

Ayni kurallar `{actionType, action, reducer}` uclulueri icin de gecerli.

### Isimlendirme

Java, jars ve beans paketlerine sahip. Ruby'nin gem'leri var. Ben bu reducer paketlerine "ducks" (ordekler) adini vermek istiyorum. "redux" ise sesdes oluyorlar boylece.

### Kullanimi

Hala sunu yapabilirsiniz:

```javascript
import { combineReducers } from 'redux';
import * as reducers from './ducks/index';

const rootReducer = combineReducers(reducers);
export default rootReducer;
```

Bunu da:

```javascript
import * as widgetActions from './ducks/widgets';
```
...boylece sadece action ureticilerini import etmis olursunuz, `bindActionCreators()` methodunda kullanima hazir olurlar.

> Aslinda, ayrica `default`'u import edecek, bu da reducer fonksiyonunuz olacak.

Bazen action creator'lar disindaki seyleri export etmek isteyeceksiniz. Kodu bozmayacaktir. Sadece action creator'lari export etmekniz gerekir diye bir kural yok sonucta. 

```javascript
import {loadWidgets, createWidget, updateWidget, removeWidget} from './ducks/widgets';
// ...
bindActionCreators({loadWidgets, createWidget, updateWidget, removeWidget}, dispatch);
```

### Ornek

[React Redux Universal Hot Example](https://github.com/erikras/react-redux-universal-hot-example) projesi "ducks" kullaniyor. Bakiniz: [`/src/redux/modules`](https://github.com/erikras/react-redux-universal-hot-example/tree/master/src/redux/modules).

[Todomvc da ducks kullaniyor.](https://github.com/goopscoop/ga-react-tutorial/tree/6-reduxActionsAndReducers)

### Implementasyon

Migrasyon aslinda oldukca [basit](https://github.com/erikras/react-redux-universal-hot-example/commit/3fdf194683abb7c40f3cb7969fd1f8aa6a4f9c57). Bence buyuk bas agrisindan kurtariyor gelistiricileri.

Herhangi bir kutuphane kullanmadan bu yaklasimi uygulamak mumkun. Ancak asagidaki implementasyonlar ve kutuphaneler araciligiyla isinizi kolaylastirabilirsiniz:

 * [extensible-duck](https://github.com/investtools/extensible-duck) - Ducks onerisinin uygulanmis hali. Bu kutuphane ile tekrardan kullanilabilir duck uretmek oldukca kolay.
 * [saga-duck](https://github.com/cyrilluce/saga-duck) - Ducks onerisinin Typescript ile uygulanmis hali. Ayrica [saga](https://github.com/redux-saga/redux-saga)'lar da dusunulmus. Tekrar kullanilabilir duck uretmek icin baska bir kutuphane.
 * [redux-duck](https://github.com/PlatziDev/redux-duck) - Redux modulleri ni ducks-modular-redux modeliyle uretmek icin yardimci bir sinif.
 * [modular-redux-thunk](https://github.com/benbeadle/modular-redux-thunk) - action, reducer, ve selector'leri organize etmenize yarayacak ducks'dan esinlenilmis bir kutuphane. redux-thunk destegi de mevcut.
 * [molecular-js](https://www.npmjs.com/package/molecular-js) - Ducks'a benzer, moduler paketler olusturmanizi saglayacak yardimci methodlardan olusan bir paket.
 * [ducks-reducer](https://github.com/drpicox/ducks-reducer) - _ducks nesnelerini_ reducer'lara donusturen bir fonksiyon.([combineReducers](https://redux.js.org/docs/api/combineReducers.html)'a es deger), ve [ducks-middleware](https://github.com/drpicox/ducks-middleware) de _ducks nesnelerinden olusan_ middleware'leri tek bir middleware'e ceviren yardimci [applyMiddleware](https://redux.js.org/docs/api/applyMiddleware.html).

Herhangi bir geri bildirimi veya problemi ana repo'ya bildirebilirsiniz veya [@erikras](https://twitter.com/erikras)'a tweet atin. Cok tesekkur ederim.

Mutlu Kodlamalar!

-- Erik Rasmussen


### Ceviriler

[한국어](https://github.com/JisuPark/ducks-modular-redux)
[中文](https://github.com/deadivan/ducks-modular-redux)
[Turkce](https://github.com/mfyz/ducks-modular-redux-tr)

---

![C'mon! Let's migrate all our reducers!](migrate.jpg)
> Foto kredisi [Airwolfhound](https://www.flickr.com/photos/24874528@N04/3453886876/).

---

[![Beerpay](https://beerpay.io/erikras/ducks-modular-redux/badge.svg?style=beer-square)](https://beerpay.io/erikras/ducks-modular-redux)  [![Beerpay](https://beerpay.io/erikras/ducks-modular-redux/make-wish.svg?style=flat-square)](https://beerpay.io/erikras/ducks-modular-redux?focus=wish)
