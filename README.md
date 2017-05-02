# Organisation App Tutorial

# Table Of Content
- [Part 1](#part-1)
  * [Pre requisite](#pre-requisite)
  * [Plan the app](#plan-the-app)
  * [Set up the Project](#set-up-the-project)
    + [Create the project with create-react-app](#create-the-project-with-create-react-app)
    + [Remove unecessary files](#remove-unecessary-files)
    + [Install Material-ui](#install-material-ui)
    + [Install React-FlexBox-Grid](#install-react-flexbox-grid)
    + [Start the local server](#start-the-local-server)
    + [Organize folder structure](#organize-folder-structure)
  * [Define Application Grid layout](#define-application-grid-layout)
  * [Create Header.js](#create-headerjs)
  * [Create a mock DB file with teams](#create-a-mock-db-file-with-teams)
  * [Display Teams](#display-teams)
  * [Implement onClick event that display the team detail](#implement-onclick-event-that-display-the-team-detail)
    + [Implement selectedTeamId](#implement-selectedteamid)
    + [Show Team Detail in a separate component](#show-team-detail-in-a-separate-component)
  * [Wrap up](#wrap-up)
    + [Commit the code to GitHub](#commit-the-code-to-github)
- [Part 2](#part-2)
  * [Set up the cloud environment](#set-up-the-cloud-environment)
    + [Create an node app with cloudant](#create-an-node-app-with-cloudant)
    + [Create Db Table for our App](#create-db-table-for-our-app)
  * [Connect the app with the DB](#connect-the-app-with-the-db)
    + [Populate the cloudant DB](#populate-the-cloudant-db)
    + [Get teams information from cloudant to react app](#get-teams-information-from-cloudant-to-react-app)
- [Part 3](#part-3)
- [Part 4](#part-4)
- [Part 5](#part-5)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>



# Part 1

## Pre requisite
- install node.js / npm
- install create-react-app
- react dev tool as browser add on (chrome in this tutorial)
- IDE for code editing with some support for react / jsx (e.g. atom, vs code, sublime, etc...)

## Plan the app
The Org App is a finctional company employee DB. 
User can create new team and add team member to it. 
Teams and team members will be added to a cloudant DB. 
User will be able to filter both teams and employee. 

For the UI we will use Material-ui and react-flexbox-grid. 

Is usefull to sketch the app before moving forward, if nothing else it helps to start to think how to irganize the app in components, how the state may look like and the actions that the app will allow. 

![app sketch](https://github.com/andreagulp/JS-Playground/blob/master/reactjs/org-chart-app/img/20170501_002133.jpg "Logo Title Text 1")

## Set up the Project

### Create the project with create-react-app
```sh
create-react-app org-chart-app
```

### Remove unecessary files
in src folder just leave App.js and index.js. 
Remove the references to deleted documents in both files. 


### Install Material-ui
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

### Install React-FlexBox-Grid
Material ui will provide the style for our elements. FlexBox grid will give us the structure of the pages 
```sh
npm install --save react-flexbox-grid
```

### Start the local server
```sh
npm start
```

### Organize folder structure
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


## Define Application Grid layout
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


## Create Header.js

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

## Create a mock DB file with teams
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


## Display Teams
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

## Implement onClick event that display the team detail
We want to allow the user to click a team card and display the team detail on the rigth side of the application.

There are different way how to implement such a function. In my case the onClick event will setState selectedTeamId.
this new property of state will inform us on which team object is currently selected. We can use the information on a .filter to only show the details of the seleted object.

Steps:
- click on team element
- fire a function that set the State for property selectedTeamId
- in render we define a selectedTeam variable to store the selected object. We will pass this property to the component showing the details of team.

### Implement selectedTeamId
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

### Show Team Detail in a separate component
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

## Wrap up
This concludes the first part of this tutorial.
We have now an app that can display data from a file and user can select team card and visualize the detail now visible in the team list.

### Commit the code to GitHub
Now that our first part is done, we will save the code in a github repository.

More detailed instruction: <https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/> 

First create a gitHub repo and call it org-app.
Copy the addres from "Clone or Download".
Your app is still running with ```npm start```, stop it by typing ctrl+c

> If you need to remove a previous orogin (this will clean it and allow to store a new remote origin
> ``` git remote rm origin ```

Steps
 1. ``` git init ```
 2. ``` git add . ```
 3. ``` git commit -m "First commit" ```
 4. ``` git remote add origin <remote repository URL> ``` 
 (e.g. ```git remote add origin https://github.com/andreagulp/org-app.git```)
 5. ``` git remote -v ```
 6. ``` git push origin master ```
 

format of push command:
``` git push  <REMOTENAME> <BRANCHNAME> ```



# Part 2
In the second part of the tutorial we will configure a cloudant DB and refactor our code to fetch data from cloudant

## Set up the cloud environment
We will use Bluemix.
Get a free account at: https://www.ibm.com/cloud-computing/bluemix/ 

### Create an node app with cloudant
Steps:
- login in bluemix
- Create App
- Choose SDK for Node.js
- Enter a name (organisation-app-001) and create...application is starting
- Once the app is up and running, go to *overview* and under connection *connect new*
- Choose Cloundant NoSQL DB and create
- Let's take also note of the credentials:
 - Open the cloudant service and go to the tab "Service credentials"
 - Click New Credential and add
 - view credential and copy its content. We will use the URL

Now you have a node server with a cloudant DB, almost ready for you to use.

### Create Db Table for our App
- On tab "Manage" , launch the DB

We have already seen that our application uses a list of team. We create a db and call it *teams*.
We also want to add team members to a team. So will create another DB called *employees*
Open each DB and copy the API link

![cloudant dashboard](https://github.com/andreagulp/org-app-tutorial/blob/master/images/2017-05-01%2023_25_53-Cloudant%20Dashboard.png)

## Connect the app with the DB
### Populate the cloudant DB
In order to make the first quik test, we will manually create 3 documents in the cloudant DB.
Take the mockDB (src/data/teams.js) that you have created previously and modify it to match json format.
it should be like this:

```json
"_id": "001",
"name": "Team number 1",
"geo": "EMEA",
"market": "Italy",
"unit": "Product 1",
"subUnit": "Sub Product 1.1",
"isActive": true
```
In cloudant dashboard open teams database and "Add New Doc", the ID is given so just add the remain part of your team object below the id:
```json
{
  "_id": "502b1129f79d716591300eddb60e7fc9",
  "name": "Team number 1",
  "geo": "EMEA",
  "market": "Italy",
  "unit": "Product 1",
  "subUnit": "Sub Product 1.1",
  "isActive": true
}
```
Click on "create document" to save it. 
repeat the operation for the remaining 2 team objects.

![teams documents](https://github.com/andreagulp/org-app-tutorial/blob/master/images/2017-05-01%2023_42_15-teams.png)

### Get teams information from cloudant to react app
We will use axios for the http calls.
Install axios
```sh
npm i --save axios
```
then import it in App.js
```javascript
import axios from 'axios'
```

in App.js create a property in the constructor to store the api url we need to read the information
```javascript
this.apiUrlTeamRead = 'https://c5bffe6c-e9b8-4b1f-ab9d-a6fab371fc10-bluemix:92ef56d8ec3c2c9c3da557ccd9a5ea727d65aadbc64597d90aa26b4b3ecef127@c5bffe6c-e9b8-4b1f-ab9d-a6fab371fc10-bluemix.cloudant.com/teams/_all_docs?include_docs=true'
```

Use axios to get the teams from cloudant
```javascript
  componentDidMount = () => {
    this.getTeams()
    }

  getTeams = () => {
    axios.get(this.apiUrlTeamRead)
      .then(response => {
        console.log(response);
      })
  }
```
If you try this code as it is, it will trown an error about CORS and authorization. Becouse this is a toy app, we will solve the issue just allowing everything :)
- Enable unauthenticated users
![enable user without authentication](https://github.com/andreagulp/org-app-tutorial/blob/master/images/2017-05-01%2023_58_05-enable%20unauthenticated%20user.png)
![enable cors](https://github.com/andreagulp/org-app-tutorial/blob/master/images/2017-05-01%2023_59_04-enable%20cors%20for%20all%20domain.png)

Now our application works, if you reload the page after the component mounts, in the console log are visible the objects we are fetching.

Next we will set the state of teams with the value from the DB. 
Notice how we need to transform the data to get a decent array of object to work with. This is a very usual thing, the data from a DB never (or maybe rarely) really fit the best scenario we would like to work with in our app. For more insight, I recommend this brilliant article: https://hackernoon.com/avoiding-accidental-complexity-when-structuring-your-app-state-6e6d22ad5e2a.

The way how to access the object in the call response, can be understood by looking at the ```response``` we logged with console.log.
![response from api call](https://github.com/andreagulp/org-app-tutorial/blob/master/images/2017-05-02%2010_56_27-access-response-from-db.png)

We are going to change our getTeams method:
```javascript
  getTeams = () => {
    axios.get(this.apiUrlTeamRead)
      .then(response => {
        console.log(response);
        this.setState({
          teams: response.data.rows.map(x => x.doc)
        })
      })
  }
```

Reaload your app and you will see that everything still works as expected, the team object is also the same, we just now have also a _rev property, that is generated by cloundant automatically together with _id.

More information about cloudant can be found online. I recommend to have a look at the following article:
- list of error code: https://docs.cloudant.com/http.html
- Api Basic: https://docs.cloudant.com/basics.html
- CRUD operations: https://docs.cloudant.com/document.html



# Part 3
Show employees associated with a team

## Populate the data of the employee DB
We have already created an employees db in the previous part of this tutorial.
Now, similarly with teams DB, we are going to add some entries to test our application.

Follow the instruction at:
*[Populate the cloudant DB](#populate-the-cloudant-db)





# Part 4
Add, Edit, Delete Operation with teams DB

# Part 5
Build and Deploy to Bluemix
