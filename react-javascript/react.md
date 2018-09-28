## Notes from Fullstack React Book:

### Steps for developing a React app from scratch:

1. Break the app into components
1. Build a static version of the app
1. Determine what should be stateful
1. Determine in which component each piece of state should live
1. Hard-code initial states
1. Add inverse data flow
1. Add server communication

### State Criteria (for step 3 above):
We can apply criteria to determine if data should be stateful:

These questions are from the excellent article by Facebook called “Thinking In React”. You can read the original article here: https://facebook.github.io/react/docs/thinking-in-react.html.

* Is it passed in from a parent via props? If so, it probably isn’t state.

> A lot of the data used in our child components are already listed in their parents. This criterion helps us de-duplicate.

> For example, “timer properties” is listed multiple times. When we see the properties declared in EditableTimerList, we can consider it state. But when we see it elsewhere, it’s not.

* Does it change over time? If not, it probably isn’t state.

> This is a key criterion of stateful data: it changes.

* Can you compute it based on any other state or props in your component? If so, it’s not state.

> For simplicity, we want to strive to represent state with as few data points as possible.”

### Determine in which component each piece of state should live (for step 4 above)

For each piece of state:

* Identify every component that renders something based on that state.
* Find a common owner component (a single component above all the components that need the state in the hierarchy).
* Either the common owner or another component higher up in the hierarchy should own the state.
* If you can’t find a component where it makes sense to own the state, create a new component simply for holding the state and add it somewhere in the hierarchy above the common owner component.

### .eslintrc installation and configuration:

Adımlar şöyle:

* Aşağıdaki komut ile eslinti ve bazı gerekli modülleri dev ortamına kur:

```sh
 npm install --save-dev eslint
 npm install --save-dev eslint-plugin-prettier
 npm install --save-dev eslint-config-prettier
 npm install --save-dev prettier
 
 # veya tek komut ile:
 npm install --save-dev eslint eslint-plugin-prettier eslint-config-prettier prettier
 
```

* eslint --init ile diğer gerekli modülleri (airbnb için) kur:

```sh
./node_modules/.bin/eslint --init
# select  below:
❯ Use a popular style guide
# after select airbnb:
❯ Airbnb (https://github.com/airbnb/javascript)
? Do you use React? (y/N) => select yes
? What format do you want your config file to be in? => select JSON
? Would you like to install them now with npm? (Y/n) => select yes
```
* it will install the required packages and create eslintrc file. Replace the contents pf the file with below:

```json
{
  "extends": ["airbnb", "prettier", "prettier/react"],
  "parser": "babel-eslint",
  "parserOptions": {
    "ecmaVersion": 8,
    "ecmaFeatures": {
      "experimentalObjectRestSpread": true,
      "impliedStrict": true,
      "classes": true
    }
  },
  "env": {
    "browser": true,
    "node": true,
    "jquery": true,
    "jest": true
  },
  "rules": {
    "no-debugger": 0,
    "no-alert": 0,
    "no-unused-vars": [
      1,
      {
        "argsIgnorePattern": "res|next|^err"
      }
    ],
    "prefer-const": [
      "error",
      {
        "destructuring": "all"
      }
    ],
    "prefer-destructuring": ["error", { "object": false, "array": false }],
    "arrow-body-style": [2, "as-needed"],
    "no-unused-expressions": [
      2,
      {
        "allowTaggedTemplates": true
      }
    ],
    "no-param-reassign": [
      2,
      {
        "props": false
      }
    ],
    "no-console": 0,
    "import/prefer-default-export": 0,
    "import": 0,
    "func-names": 0,
    "space-before-function-paren": 0,
    "comma-dangle": 0,
    "max-len": 0,
    "import/extensions": 0,
    "no-underscore-dangle": 0,
    "consistent-return": 0,
    "react/display-name": 1,
    "react/no-array-index-key": 0,
    "react/react-in-jsx-scope": 0,
    "react/prefer-stateless-function": 0,
    "react/forbid-prop-types": 0,
    "react/no-unescaped-entities": 0,
    "react/no-multi-comp": 0,
    "react/prop-types": 0,
    "jsx-a11y/click-events-have-key-events": 0,
    "jsx-a11y/no-static-element-interactions": 0,
    "react/destructuring-assignment": 0,
    "jsx-a11y/accessible-emoji": 0,
    "react/jsx-filename-extension": [
      1,
      {
        "extensions": [".js", ".jsx"]
      }
    ],
    "radix": 0,
    "no-shadow": 0,
    "quotes": [
      2,
      "single",
      {
        "avoidEscape": true,
        "allowTemplateLiterals": true
      }
    ],
    "prettier/prettier": [
      "error",
      {
        "trailingComma": "es5",
        "singleQuote": true,
        "printWidth": 100
      }
    ],
    "jsx-a11y/href-no-hash": "off",
    "jsx-a11y/anchor-is-valid": [
      "warn",
      {
        "aspects": ["invalidHref"]
      }
    ]
  },
  "plugins": ["prettier"]
}

```

* eğer global olarak tek seferde kurmak  istiyorsak :

```sh
npm i -g eslint eslint-plugin-import eslint-plugin-react eslint-config-airbnb eslint-plugin-jsx-a11y eslint-plugin-prettier babel-eslint
```

bu durumda her seferinde yukarıdaki adımları tekrarlamaya gerek yok. Sadece proje root klasörüne .eslintrc.json dosyası oluşturup yukarıdakileri yapıştırmak yeterli...
 
