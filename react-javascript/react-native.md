# React Native + Django:
### [A Blog Post about React Native + Django](http://kindshofer.net/2016/04/07/from_the_web_to_the_app_django_restframework_oauth2_and_reactnative.html)
### [Sharing Code between React Web and Native Apps](http://jkaufman.io/react-web-native-codesharing/)


# Configure ESLint in Atom:

- Install linter and linter-eslint packages from the Atom->Preferences->Install
- Install rallycoding configuration in the project directory:
 
```sh 
$ npm install --save-dev eslint-config-rallycoding
```
- create .eslintrc file in the project root and put the following content:

```javascript
{
  "extends": "rallycoding"
}

```

- repeat 2 and 3 for every project.

- ### [ESLint setup for SublimeText3](https://www.udemy.com/the-complete-react-native-and-redux-course/learn/v4/t/lecture/5744876)