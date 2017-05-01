# Organisation App Tutorial

# Table Of Content
- [Pre requisite](#pre-requisite)
- [Plan the app](#plan-the-app)
- [Set up the Project](#set-up-the-project)
  * [Create the project with create-react-app](#create-the-project-with-create-react-app)
  * [Remove unecessary files](#remove-unecessary-files)
  * [Install Material-ui](#install-material-ui)
  * [Install React-FlexBox-Grid](#install-react-flexbox-grid)
  * [Start the local server](#start-the-local-server)
  * [Organize folder structure](#organize-folder-structure)
- [Define Application Grid layout](#define-application-grid-layout)
- [Create Header.js](#create-headerjs)
- [Create a mock DB file with teams](#create-a-mock-db-file-with-teams)
- [Display Teams](#display-teams)
- [Implement onClick event that display the team detail](#implement-onclick-event-that-display-the-team-detail)
  * [Implement selectedTeamId](#implement-selectedteamid)
  * [Show Team Detail in a separate component](#show-team-detail-in-a-separate-component)
- [Wrap up](#wrap-up)
  * [Commit the code to GitHub](#commit-the-code-to-github)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


# Pre requisite
- install node.js / npm
- install create-react-app
- react dev tool as browser add on (chrome in this tutorial)
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


# Define Application Grid layout
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

# Create a mock DB file with teams
Let's create a file that will mock a db with teams and users information. 
In later stage we will replace this mock with a real cloudant DB. 
For the moment I just want to go ahead and create some other components before I start to work with a real DB and http calls. 

For convinience I'll create a folder "src/data" to store the mock DBs. 
In src/data/teamsDB.js 
```javascript
const TEAMS = [
  {
    _id: '001',
    name: 'Team number 1',
    geo: 'EMEA',
    market: 'Italy',
    unit: 'Product 1',
    subUnit: 'Sub Product 1.1',
    isActive: true
  },
  {
    _id: '002',
    name: 'Team number 2',
    geo: 'EMEA',
    market: 'France',
    unit: 'Product 2',
    subUnit: 'Sub Product 2.2',
    isActive: true
  },
  {
    _id: '003',
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
If you open Chrome devopers tool and select react, you can inspect the component created and see if teams array is available.
![chrome dev tool with react extension](https://github.com/andreagulp/org-app-tutorial/blob/master/images/2017-05-01%2002_52_08-React%20App.png)


# Display Teams
We will create a TeamList component and a Team component to display them. So let's create both file in components folder.

in src/components/TeamList.js
```javascript
import React from 'react';
import {List} from 'material-ui/List';
import Team from './Team';

const TeamList = (props) => {
  return (
    <List>
      {props.teams.map(team => {
        return (
          <Team
            key={team._id}
            team={team}
          />
        )
      })}
    </List>
  )
};
export default TeamList
```

In src/components/Team.js
```javascript
import React, { Component } from 'react';
import Divider from 'material-ui/Divider';
import {ListItem} from 'material-ui/List';

class Team extends Component {

  render () {

    return (
        <div>
          <ListItem
            primaryText={this.props.team.name}
            secondaryText={this.props.team.geo}
            secondaryTextLines={1}
          />
          <Divider />
        </div>
    )
  }
};
export default Team
```

In src/containers/App.js, pass teams as prop
```javascript
import TeamList from '../components/TeamList';
```

```javascript
  <Col xs={6} md={6}>
    <TeamList
      teams={this.state.teams}
    />
  </Col>
 ```
 We are now displaying the teams in a list with some basic (but nice) style

# Implement onClick event that display the team detail
We want to allow the user to click a team card and display the team detail on the rigth side of the application.

There are different way how to implement such a function. In my case the onClick event will setState selectedTeamId.
this new property of state will inform us on which team object is currently selected. We can use the information on a .filter to only show the details of the seleted object.

Steps:
- click on team element
- fire a function that set the State for property selectedTeamId
- in render we define a selectedTeam variable to store the selected object. We will pass this property to the component showing the details of team.

## Implement selectedTeamId
How to build this in react:
- in App.js add selectedTeamId to the state
```javascript
    this.state = {
      teams: TEAMS,
      selectedTeamId: ''
    }
```
- In App.js create a setSelectedTeamId method, that takes an ID and sets the state to that ID
```javascript
  setSelectedTeamId = (id) => {
    this.setState({
      selectedTeamId: id
    })
  }
```
- In App.js pass the method to TeamList
```javascript
            <Col xs={6} md={6}>
              <TeamList
                teams={this.state.teams}
                setSelectedTeamId={this.setSelectedTeamId}
              />
            </Col>
 ```
 - In TeamList.js pass to Team setSelectedTeamId and also create a unique key based on the team ID that we will call for convinience index
```javascript
          <Team
            key={team._id}
            index={team._id}
            team={team}
            setSelectedTeamId={props.setSelectedTeamId}
          />
```
- In Team.js add an onClick event
```javascript
          <ListItem
            primaryText={this.props.team.name}
            secondaryText={this.props.team.geo}
            secondaryTextLines={1}
            onClick={this.setSelectedTeamId}
          />
```
Now you can see (in chrome react dev tools) that our new state property selectedTeamId, changes when we click to a new line.

## Show Team Detail in a separate component
Now that we can get the ID of the selected object, we can create a variable to filter the selected object and pass it to TeamDetail.js

- Create another component to hold the team detail. We will call it TeamDetail.js. After you have created an empty component import it in App.js

- Create a variable in App.js render method to filter our selected team. Note that you could create the selectedTeam as property of the state. However such an approach will not work becouse of the asynch nature of the react state.
```javascript
let selectedTeam = this.state.teams.filter(team => team._id === this.state.selectedTeamId)[0]
```
- In App.js pass the selectedTeam variable to TeamDetail component
```javascript
            <Col xs={6} md={6}>
              <TeamDetail
                selectedTeam={selectedTeam}
              />
            </Col>
 ```
 - Implement team detail. Is important to note that teamDetail component is suppose to show something that doesn't exist as soon as the state is initialized, in effect only after we click on something the TeamDetail will know which Team we want toi visualize. we need to add some conditional rendering to fix this issue
```javascript
import React, { Component } from 'react';

class TeamDetail extends Component {

  render () {
    if (!this.props.selectedTeam) {
      return (
        <p>Select a Team to see the its details</p>
      )
    }
    return (
      <div>
        <h3>{this.props.selectedTeam.name}</h3>
        <p>{this.props.selectedTeam.geo}</p>
        <p>{this.props.selectedTeam.market}</p>
        <p>{this.props.selectedTeam.unit}</p>
        <p>{this.props.selectedTeam.subUnit}</p>
        <p>{this.props.selectedTeam.isActive}</p>
      </div>
    )
  }
};
export default TeamDetail
```

# Wrap up
This concludes the first part of this tutorial.
We have now an app that can display data from a file and user can select team card and visualize the detail now visible in the team list.

## Commit the code to GitHub
Now that our first part is done, we will save the code in a github repository.











