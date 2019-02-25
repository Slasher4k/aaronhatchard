---
layout: react
title: "Fairtasker: Airtasker Clone"
metadescription: "Create an Airtasker clone with React"
---

{::options auto_ids="false" /}

#### (Part 1)

To practice our React skills we are going to be building a simple clone of the airtasker website, specifically the [browse tasks][tasks]{: .white .white-hover-dots rel="nofollow"} page. 

[![Highlighted Airtasker components][airtasker-components-img]{: .u-responsive-img}][airtasker-components-img]

Having looked at the browse tasks page we can start to workout the initial components we are going to need for our app. The coloured rectangles are going to become the following components:

* Layout: Black
* Navigation Menu: Blue
* Tasks: Red
* TasksLeft: Purple
* TaskCard: Yellow
* TasksRight: Green 
* TaskDetails: Orange
{: style="list-style: inside; "}

There is one more component that we need but is not shown in the image, `TasksMap`,  it switches places with the `TasksDetail` component when no task is seleceted.

On smaller screens we also see that only one component, either the `TasksLeft` component or the `TasksRight` component containing `TaskDetails` is shown at a time and that `TasksMap` is never shown.

For our design we are going to use the material design pattern provided by [material-ui][material-ui]{: .white .white-hover-dots rel="nofollow"} and to make our components responsive we will use the [react-responsive][react-responsive]{: .white .white-hover-dots rel="nofollow"} module.

## Initial Setup
{: .dotted .white-dots}

Knowledge requirements:

* Javascript (ES6)
* NPM/Yarn (I'll be using npm in the tutorial)
* React
{: style="list-style: inside;"}


The first thing we need to do is create our app using create-react-app, if you haven't done so you will first need to install create-react-app via npm `npm install -g create-react-app`, you can call the project anything you like but I'm going with 'fairtasker'. Once the project is setup we will go ahead and install the other dependancies material-ui, react-responsive and react-router-dom to add naviagtion to our app.

~~~
create-react-app fairtasker
cd fairtasker
npm install react-responsive @material-ui/core react-router-dom --save
~~~

Now it's time to remove some of the redundant code from the project before we start adding our own.

First we will delete following files from the src directory:

* App.css
* App.test.js
* logo.svg
* serviceWorker.js
{: style="list-style: inside;"}

Next we need to remove some imports from the `App.js` and `index.js`. From `App.js` we will remove the logo and css imports. While we're in the `App.js` file we will replace the contents of the main `div` with `"Hello World"`

~~~
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div className="App">
        Hello World
      </div>
    );
  }
}

export default App;
~~~

From `index.js` we will remove the `serviceWorker` import and it's `unregister()` function call at the bottom along with it's comments.

~~~
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
~~~

To support our use of the Material design pattern provided by the `material-ui` module we need to include the Roboto font, to do this we will go the route of adding a link to the `index.html` file found in the `public`{: .language-none} directory, if you want to you can also [install it via npm][roboto-npm]{: .white .white-hover-dots rel="nofollow"}

~~~
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500"> 
~~~

While we're  in the `index.html` file go ahead and update the title of our app to `Fairtasker`

~~~
<title>Fairtasker</title>
~~~

The final step before we begin adding our components is adding some additional folders to the `src` directory, we'll add:

* components: Contains our functional components
* containers: Contains our stateful components
{: style="list-style: inside;"}

Run the command `npm run start` and it will open a broswer at `http://localhost:3000/`{: .language-none} where we will see out running app displaying `Happy World` to us. Now that our initial setup is working we will start adding our own components.


## Creating Components
{: .dotted .white-dots}

When it comes to creating components I like to work my way in from the outside, as seen in the image earlier, we will start on the biggest rectangle and work in. We'll first create the components with placeholder content and once we have them rendering correctly we will go through and fill them out with componenets from `material-ui` and dummy data to get us closer to our final look.

We already have `App` component which will contain all new ones so we will start with our general layout component that will position the navigation bar at the top and render any children passed to it. 

Add the folder `Layout` to the `components` directory and create the `Layout.js` file inside of it, this will be a functional component.

~~~
// ./components/Layout/Layout.js

import React from 'react';

const Layout = (props) => {
    return ( <div>
        <div>Navbar</div>
        {props.children}
    </div> );
}
 
export default Layout;
~~~

We could split the navigation bar off as it's own component but for now it will be a simple navigation bar so we will leave it as is, depending how we go we might seperate it out in the future.

Next up is the overall `Tasks` container which will manage the tasks and associated data and pass relevant data to the `TasksLeft` and `TasksRight` components.

Add the `Tasks` folder to `containers` and create `Tasks.js`, this is a stateful component and will import and render `<TasksLeft />` and `<TasksRight />` 


~~~
// ./containers/Tasks/Tasks.js

import React, { Component } from 'react';
import TasksLeft from '../../components/TasksLeft/TasksLeft';
import TasksRight from '../../components/TasksRight/TasksRight';

class Tasks extends Component {
    state= { };

    render() { 
        return ( <div>
                    <TasksLeft />
                    <TasksRight />
                </div> );
    }}
 
export default Tasks;
~~~

This will obviously cause an error as `TasksLeft` and `TasksRight` don't exist yet so let's create them now starting with `TasksLeft`.

`Tasksleft` is another functional component which will be passed a collection of tasks, we will mock it in the component for now, and will show a `TaskCard` for each.

Add the `TasksLeft` component to the `components` directory, import `TaskCard` and render an arbitrary list of them. 

~~~
// ./components/TasksLeft/TasksLeft.js

import React from 'react';
import TaskCard from "./TaskCard/TaskCard"

const TasksLeft = (props) => {
    const cards = Array(5).fill(<TaskCard />)

    return (<div>{cards}</div> );
}
 
export default TasksLeft;
~~~

Notice the import path for `TaskCard` is relative to the `TaksLeft` file, not the `components` folder, because it will only be used as a child of `TasksLeft`, for this reason it will be created in the `TasksLeft` folder, do that now.

~~~
// ./components/Tasksleft/TaskCard/TaskCard.js

import React from 'react';

const TaskCard = (props) => {
    return ( <div>Task Card</div> );
}
 
export default TaskCard;
~~~

That is all for `TasksLeft`, now to work on `TasksRight`.

`TasksRight` will have two different components that will only be shown, either `TasksMap` if no task is selected or `TaskDetails` if a task is selected. `TasksMap` and `TaskDetails` will only be used by `TasksRight` so they will also be in subfolders of the `TasksRight` folder.


~~~
// ./components/TasksRight/TasksRight.js

import React from 'react';
import TasksMap from './TasksMap/TasksMap';
import TaskDetails from './TaskDetails/TaskDetails';

const TasksRight = (props) => {
    return (<div>
        <TasksMap />
        <TaskDetails />
    </div> );
}
 
export default TasksRight;
~~~

~~~
// ./components/TasksRight/TasksMap/TasksMap.js

import React from 'react';

const TasksMap = (props) => {
    return ( <div>Task Map</div> );
}

export default TasksMap;
~~~

~~~
// ./components/TasksRight/TaskDetails/TaskDetails.js

import React from 'react';

const TaskDetails = (props) => {
    return ( <div>Task Details</div> );
}
 
export default TaskDetails;
~~~

Now that all the components are created we can import `Layout` and `Tasks` into our `App` component and render `Tasks` as a child of `Layout`.
~~~
// ./App.js

import React, { Component } from 'react';
import Layout from './components/Layout/Layout';
import Tasks from './containers/Tasks/Tasks';

class App extends Component {
  render() {
    return ( <Layout>
                <Tasks />
            </Layout>  );
  }
}

export default App;
~~~

If we revisit our app in the browser we will see all our components looking something like this:

![Fairtasker initial component design][fairtasker-img]{: .u-responsive-img style="margin: 0 auto;"}

## Add Material UI
{: .dotted .white-dots}

It's great that our components are working but the layout and design has a bit of room for improvement so let's get start making use of the `material-ui` components in our components to speed up the construction of our app.

If you would like to understand what the props that we pass to the `material-ui` components are doing, visit their corresponing API pages: [Grid][MI-Grid]{: .white .white-hover-dots rel="nofollow"}, [Appbar][MI-Appbar]{: .white .white-hover-dots rel="nofollow"}, [Typography][MI-Typography]{: .white .white-hover-dots rel="nofollow"}

In `Layout` we will put our navigation bar, made with the `AppBar` component and render the children passed in via props.

We will be using the spacing property of `Grid` which can produce [unwanted horizontal scrolling][negative-margins]{: .white .white-hover-dots rel="nofollow"}, we will take the approach of wrapping the `props.children` in a `div` with some padding to fix the horizontal scrolling issue. 

{% assign style="{{ padding: '8px' }}" %}

~~~
// ./components/Layout/Layout.js

import React from 'react';
import AppBar from '@material-ui/core/AppBar';
import Toolbar from '@material-ui/core/Toolbar';
import Typography from '@material-ui/core/Typography';

const Layout = ({ children }) => {
    return (<><AppBar position="static">
                    <Toolbar>
                        <Typography variant="h6" >
                            Fairtakser
                        </Typography>
                    </Toolbar>
                </AppBar>
                <div style={{ style }}>                
                    {children}           
                </div>
            </>)
}
 
export default Layout;
~~~

Next up is the `Tasks` container, in our `state` we will have some dummy tasks (`tasks`) that will be passed to `TasksLeft` and a selected task (`selectedTask`) that will go to `TasksRight`.

~~~
// ./containers/Tasks/Tasks.js

import React, { Component } from 'react';
import TasksLeft from '../../components/TasksLeft/TasksLeft';
import TasksRight from '../../components/TasksRight/TasksRight';
import Grid from '@material-ui/core/Grid'

class Tasks extends Component {
    state = {
        selectedTask: false,
        tasks: Array(8).fill(0).map( (_, i) => ({
            id: '' + i,
            title: `Task ${i}`,
            description: `I am task ${i}`,
            status: "Completed" }))
    }

    render() { 
        const {tasks, selectedTask} = this.state;

        if(!selectedTask){
            setTimeout(() => {
                this.setState(({ tasks }) => ({ selectedTask: tasks[0] }))
            }, 3000);
        }

        return ( <Grid container spacing={16} justify="center">
                    <TasksLeft tasks={tasks} />
                    <TasksRight task={selectedTask} />
                </Grid>);
    }}
 
export default Tasks;
~~~

The task id is turned into a string to used as the key when the array of `TaskCards` is created in `TasksLeft`.

Just to show the switching of the `TasksMap` and `TaskDetails` components when there is a selected task I have put the following snippet into `render()`:

~~~
if(!selectedTask){
    setTimeout(() => {
        this.setState(({ tasks }) => ({ selectedTask: tasks[0] }))
    }, 3000);
}
~~~

On the first render of `TasksRight` we will see `TasksMap` and set a timer for 3 seconds after which `selectedTask` is set to the first of our `tasks` and `TasksMap` changes to `TaskDetails`.

For `TasksLeft` we need to replace `Array.fill()` with a mapping of tasks from `props.tasks` to `TaskCards` and pass in the `task`.

~~~
// ./components/TasksLeft/TasksLeft.js

import React from 'react';
import TaskCard from "./TaskCard/TaskCard"
import Grid from '@material-ui/core/Grid'

const TasksLeft = ({ tasks }) => {
    const cards = tasks.map(task => <TaskCard key={task.id} task={task} />)

    return (<Grid item xs={3}>{cards}</Grid> );
}
 
export default TasksLeft;
~~~

For `TaskCards` we will use a `Card` component and output the task information along with some static placeholder information to match the Airtasker task cards.

~~~
// ./components/Tasksleft/TaskCard/TaskCard.js

import React from 'react';
import Card from '@material-ui/core/Card';
import CardContent from '@material-ui/core/CardContent';
import Typography from '@material-ui/core/Typography';

const TaskCard = ({ task }) => {
    
    return ( <Card>
        <CardContent>
            <Typography variant="h6">
                {task.title}
            </Typography>
            <Typography variant="body1">
                {task.description}
            </Typography>
            <Typography variant="caption">
                location
            </Typography>
            <Typography variant="caption">
                date
            </Typography>
            <Typography variant="caption">
                time
            </Typography>
            <hr />
            <Typography variant="caption">
                {task.status} - 1 offers
            </Typography>
        </CardContent>
    </Card> );
}
 
export default TaskCard;
~~~

For `TasksRight` we will again use a `Grid` and conditionally render within it either `TaskDetails` (passing on the `task`) or `TasksMap` based on if it is passed a task in it's `props`.

~~~
// ./components/TasksRight/TasksRight.js

import React from 'react';
import TasksMap from './TasksMap/TasksMap';
import TaskDetails from './TaskDetails/TaskDetails';
import Grid from '@material-ui/core/Grid'

const TasksRight = ({ task }) => {
    
    return (<Grid item xs={4}>
        { task ? <TaskDetails task={task} /> : <TasksMap /> }
    </Grid> );
}
 
export default TasksRight;
~~~

For now `TaskDetails` will use `Card` to output the task title and description. (We will add more to this component in the future.)

~~~
// ./components/TasksRight/TaskDetails/TaskDetails.js

import React from 'react';
import Card from '@material-ui/core/Card'
import CardContent from '@material-ui/core/CardContent';
import Typography from '@material-ui/core/Typography'

const TaskDetails = ({ task }) => {

    return <Card>
        <CardContent>
            <Typography variant="headline">
                {task.title}
            </Typography>
            <Typography variant="body1">
                {task.description}
            </Typography>
        </CardContent>
    </Card>;
}

export default TaskDetails;
~~~

`TasksMap` will eventually have an interactive map showing the location of tasks but for now we will simply show a card with placeholder text.

~~~
// ./components/TasksRight/TasksMap/TasksMap.js

import React from 'react';
import Card from '@material-ui/core/Card'
import CardContent from '@material-ui/core/CardContent';
import Typography from '@material-ui/core/Typography'

const TaskMap = ({ task }) => {

    return <Card>
        <CardContent>
            <Typography variant="headline">
                Tasks Map
            </Typography>
           
        </CardContent>
    </Card>;
}

export default TaskMap;
~~~ 

With all the components updated to include `material-ui` components, if we now visit the app we should have something that looks like:

[![Fairtasker initial material ui design][fairtasker-final-img]{: .u-responsive-img}][fairtasker-final-img]

## The End
{: .dotted .white-dots}

Phew! That was a lot of typing (for me at least). We have done a good amount of work and our app is starting to take shape. We still have some minor CSS additions to make to bring the design more in line with Airtasker, like adding a coloured side and spacing around the `TaskCards` and adding a backgound colour to the page so the cards standout. When the screen starts to get smaller `TasksLeft` begins to get squished so we will need some minimum widths to prevent that and instead of the whole page being scrollable when we have a lot of `TaskCards` we will make the `TasksLeft` and `TasksRight` independently scrollable. 

In regards to functionality we have a couple of things to do, we don't want to rely on the timer to select a task for us so we will use `react-router-dom` to enable us to click on a `TaskCard` and be shown the details in `TaskDetails`. When it comes to smaller screens it is impractical to show both `TasksLeft` and `TasksRight` so we will use `react-responsive` to show only one at a time.

Now that we know the CSS and functionality additions that need to be made be sure to tune in next time as I walk you through how we do that.


[tasks]:https://www.airtasker.com/tasks/
[material-ui]:https://material-ui.com
[react-responsive]:https://github.com/contra/react-responsive
[roboto-npm]:https://material-ui.com/style/typography/#install-with-npm
[negative-margins]:https://material-ui.com/layout/grid/#limitations
[fairtasker-img]: /images/fairtasker-part1.png "Fairtasker initial component design"
[fairtasker-final-img]: /images/fairtasker-final1.png "Fairtasker initial material ui design"
[airtasker-components-img]: /images/airtasker-components.png "Airtasker component blueprint"
[MI-Grid]:https://material-ui.com/api/grid/
[MI-Appbar]:https://material-ui.com/api/app-bar/
[MI-Typography]:https://material-ui.com/api/Typography/

<script src="/js/prism.js">