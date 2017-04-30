# org-app-tutorial

# Tutorial

# Plan the app
The Org App is a finctional company employee DB.
User can create new team and add team member to it.
Teams and team members will be added to a cloudant DB.
User will be able to filter both teams and employee.

For the UI we will use Material-ui and react-flexbox-grid.

Is usefull to sketch the app before moving forward, if nothing else it helps to start to think how to irganize the app in components, how the state may look like and the actions that the app will allow.

![app sketch] (https://github.com/andreagulp/JS-Playground/blob/master/reactjs/org-chart-app/img/20170501_002133.jpg)

# Set up the Project

## Create the project with create-react-app
```sh
create-react-app org-chart-app
```

## Remove unecessary files
in src folder just leave App.js and index.js
Remove the references to deleted documents in both files.


## Install Material-ui
Install material-ui
```sh
npm i --save material-ui react-tap-event-plugin
```
to be able to use it we need to set in src/index.js
```javascript
import injectTapEventPlugin from 'react-tap-event-plugin';
injectTapEventPlugin();
```

and also wrap your full application in material ui. In this case App.js will contain the while application

```javascript
import React, { Component } from 'react';

import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';

class App extends Component {
  render() {
    return (
      <MuiThemeProvider>
        <p>Hello App</p>
      </MuiThemeProvider>
    );
  }
}

export default App;
```

# Start the Project

