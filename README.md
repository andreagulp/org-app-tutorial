# Organisation App Tutorial

# Pre requisite
- install node.js / npm
- install create-react-app
- react dev tool as browser add on
- IDE for code editing with some support for react / jsx (e.g. atom, vs code, sublime, etc...)

# Plan the app
The Org App is a finctional company employee DB.
User can create new team and add team member to it.
Teams and team members will be added to a cloudant DB.
User will be able to filter both teams and employee.

For the UI we will use Material-ui and react-flexbox-grid.

Is usefull to sketch the app before moving forward, if nothing else it helps to start to think how to irganize the app in components, how the state may look like and the actions that the app will allow.

![app sketch](https://github.com/andreagulp/JS-Playground/blob/master/reactjs/org-chart-app/img/20170501_002133.jpg "Logo Title Text 1")

# Set up the Project

## Create the project with create-react-app
```sh
create-react-app org-chart-app
```

## Remove unecessary files
in src folder just leave App.js and index.js. 
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

## Install React-FlexBox-Grid
Material ui will provide the style for our elements. FlexBox grid will give us the structure of the pages
```sh
npm install --save react-flexbox-grid
```

## Start the local server
```sh
npm start
```

## Organize folder structure
We will use the distincion between container and presentation components, so let's go ahead and create these folders. We will also move App.js inside containers folder (make sure you change the refence to App.js inside src/index.js.

```
-src
  -components
  -containers
    -App.js
  -index.js
```

Then in src/index.js change the import of App.js to:
```javascript
import App from './containers/App';
```

# Create Header.js

Our app will have an Header. We are using material ui App Bar.
Always remember to wire the components to our App.js container.

In src/components/Header.js
```javascript
import React from 'react';
import AppBar from 'material-ui/AppBar';
import IconButton from 'material-ui/IconButton';
import MoreVertIcon from 'material-ui/svg-icons/navigation/more-vert';
import IconMenu from 'material-ui/IconMenu';
import MenuItem from 'material-ui/MenuItem';

const Header = (props) => {
  return (
  <AppBar
    title="Organization App"
    showMenuIconButton={false}
    iconElementRight={
      <IconMenu
        iconButtonElement={
          <IconButton
            touch={true}
            tooltip="actions"
            tooltipPosition="bottom-left"
          >
            <MoreVertIcon />
          </IconButton>
        }
      >
        <MenuItem primaryText="Add New Squad" />
        <MenuItem primaryText="Action 2" />
        <MenuItem primaryText="Action 3" />
      </IconMenu>
    }
  />
  )
};
export default Header
```

In src/containers/App.js
```javascript
import React, { Component } from 'react';
import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';

import Header from '../components/Header';

class App extends Component {
  render() {
    return (
      <MuiThemeProvider>
        <div>
          <Header />
        </div>
      </MuiThemeProvider>
    );
  }
}

export default App;
```

# Define Application layout
I find it easier to start by defining the boxes where the element of the app will fit.
We will introduce flex box now.

We have already installed the module, so we just import it in App.js and create the layout.

```javascript
import React, { Component } from 'react';
import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';
import { Grid, Row, Col } from 'react-flexbox-grid';

import Header from '../components/Header';

class App extends Component {
  render() {
    return (
      <MuiThemeProvider>
        <Grid fluid>
          <Row>
            <Header />
          </Row>
          <Row>
            <Col xs={6} md={6}>
              <p>First Column with Team List</p>
            </Col>
            <Col xs={6} md={6}>
              <p>Second Column with Team Details</p>
            </Col>
          </Row>
        </Grid>
      </MuiThemeProvider>
    );
  }
}

export default App;
```

# Create a mock DB file with teams
Let's create a file that will mock a db with teams and users information.
In later stage we will replace this mock with a real cloudant DB.
For the moment I just want to go ahead and create some other components before I start to work with a real DB and http calls.

For convinience I'll create a folder "src/data" to store the mock DBs.
In src/data/teamsDB.js
```javascript
const TEAMS = [
  {
    id: '001',
    name: 'Team number 1',
    geo: 'EMEA',
    market: 'Italy',
    unit: 'Product 1',
    subUnit: 'Sub Product 1.1',
    isActive: true
  },
  {
    id: '002',
    name: 'Team number 2',
    geo: 'EMEA',
    market: 'France',
    unit: 'Product 2',
    subUnit: 'Sub Product 2.2',
    isActive: true
  },
  {
    id: '003',
    name: 'Team number 3',
    geo: 'EMEA',
    market: 'Italy',
    unit: 'Product 3',
    subUnit: 'Sub Product 3.3',
    isActive: true
  }
]

export default TEAMS
```

in src/components/App.js import TEAMS and use in state

```javascript
import TEAMS from '../data/teamsDB';
```
```javascript
  constructor (props) {
    super (props);
    this.state = {
      teams: TEAMS
    }
  }
```











