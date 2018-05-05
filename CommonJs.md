## Common JS Ornegi

```javascript
// widgets.js

const LOAD   = 'my-app/widgets/LOAD';
const CREATE = 'my-app/widgets/CREATE';
const UPDATE = 'my-app/widgets/UPDATE';
const REMOVE = 'my-app/widgets/REMOVE';

function reducer(state = {}, action = {}) {
  switch (action.type) {
    // do reducer stuff
    default: return state;
  }
}

reducer.loadWidgets = function() {
  return { type: LOAD };
}

reducer.createWidget = function(widget) {
  return { type: CREATE, widget };
}

reducer.updateWidget = function(widget) {
  return { type: UPDATE, widget };
}

reducer.removeWidget = function(widget) {
  return { type: REMOVE, widget };
}

module.exports = reducer;
```


Duck paketlerinin icinde Redux'un `bindActionCreators()` methodunu dogrudan kullanamamizin ana nedeni, herhangi bir fonsiyon tanimlamasini, tek bir action creator olarak varsaymasi. Bundan dolayi soyle birsey yapmaniz gerekiyor:

```javascript
var actionCreators = require('./ducks/widgets');
bindActionCreators({ ...actionCreators });
```

Baska bir negatif yan da, tur sabitlerini export ederken, buna iliskili reducer fonksiyonu da eklemeniz gerekiyor. Yani action creator'lari baska bir objeye tasiyamiyorsunuz kolayca.

Bunlarin hepsinden kacmak kolay. Kendi dispatch binding fonksiyonunuzu yazmak - asagidaki ornek `mapDispatchToProps` olarak gonderdigimiz bir fonksiyonu uretip `connect()`e arguman olarak gonderiyor:

```javascript
/**
 * Creates a function which creates same-named action dispatchers from an object
 * whose function properties are action creators. Any non-functions in the actionCreators
 * object are ignored.
 */
var createActionDispatchers = actionCreators => dispatch =>
  Object.keys(actionCreators).reduce((actionDispatchers, name) => {
    var actionCreator = actionCreators[name];
    if (typeof actionCreator == 'function') {
      actionDispatchers[name] = (...args) => dispatch(actionCreator(...args));
    }
    return actionDispatchers;
  }, {})

var actionCreators = require('./ducks/widgets');
var mapStateToProps = state => state.widgets;
var mapDispatchToProps = createActionDispatchers(actionCreators);

var MyComponent = React.createClass({ /* ... */ });

module.exports = connect(mapStateToProps , mapDispatchToProps)(MyComponent);
```

---
Bu dokuman [@insin](https://github.com/insin) tarafindan acilan github tartismasindan alintidir [#2](https://github.com/erikras/ducks-modular-redux/issues/2).
