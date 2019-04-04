---
layout: react
title: "Fairtasker: firebase backend"
metadescription: "Add firebase realtime database backend to fairtakser for persisiting task data and fetching saved tasks"
---

Now that we have the basics of our layout setup after the first two tutorials it's time to take the app up a notch and stop generating the tasks every time the app is loaded, we want to be able to create, persist and load the created tasks, to do this we will be using simple datastorage made available by the firebase realtime database. The realtime database provides a restful API that we will use to save and fetch our data, it doesn't provide advanced query options but as this is just a demo application it will suit our needs. 

#### Knowledge requirements
{: .dotted .white-dots}

* Firebase
* Axios
{: style="list-style: inside;"}

### Setup firebase backend
{: .dotted .white-dots}

Lets get started by first getting the url to a firebase realtime database, head on over to [firebase](https://firebase.google.com/){: .white .white-hover-dots rel="nofollow"} and create a new project. If you haven't already got a firebase account it's free for what we are going to be doing so once you're there sign up and create a project. Name the project whatever you like, I went with `fairtasker`, and once the project is created you will see the option `Add Firebase to your web app` clicking on this will present you will the code that can be copied to your site, we don't need all of it, just copy the `databaseURL` and save it as an export in our constants file `export const DB_BASE_URL = 'https://example.firebaseio.com/';`.

To see the setting up visually, watch [this video](https://youtu.be/PP4Tr0l08NE?t=60){: .white .white-hover-dots rel="nofollow"}, for around 50 seconds.

### Seed database
{: .dotted .white-dots}

Now that we have a database we need to create a collection of tasks and store them in said database. I am going to save you some time and have created a small little app that will seed a firebase database with 50 randomly generated tasks, all you need to do is provide the name of your project, the part after `https://` and before `.firebaseio.com/`, click `Seed` and then sit back and wait. Once the database is seeded you will see the response `Firebase has been seeded` and you can move on to the next part.

<iframe src="https://codesandbox.io/embed/pqv5l572m?fontsize=14&view=preview" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

The new tasks contain the additional fields `price`, `postedBy`, `date` and `time` which we will make use of later in the tutorial.

### Show and tell
{: .dotted .white-dots}

As we are now going to be getting our tasks asyncronously we won't have tasks to display on initial load. To prevent user's from thinking that the site is broken we will indicate that something is happening by showing a spinner while the required data is being fetched.

Create a `UI` folder in components and add a new file `Spinner.js` and CSS module `Spinner.module.css`. For our `Spinner` we will get the HTML and CSS from [css spinner][css-spinner]{: .white .white-hover-dots rel="nofollow"}, we simply need to choose one we like, view it's source and copy the HTML into our component and the CSS into our CSS file.

~~~
// ./components/Spinner/Spinner.js

import React from 'react';
import classes from "./Spinner.module.css";

const Spinner = () => <div className={classes.loader}>Loading...</div>;
 
export default Spinner;
~~~

~~~
/* ./components/UI/Spinner/Spinner.module.css */

.loader,
.loader:before,
.loader:after {
  border-radius: 50%;
}

.loader {
  color: #8ecfe8;
  font-size: 11px;
  text-indent: -99999em;
  margin: 55px auto;
  position: relative;
  width: 10em;
  height: 10em;
  box-shadow: inset 0 0 0 1em;
  -webkit-transform: translateZ(0);
  -ms-transform: translateZ(0);
  transform: translateZ(0);
}

.loader:before,
.loader:after {
  position: absolute;
  content: '';
}

.loader:before {
  width: 5.2em;
  height: 10.2em;
  background: #E9EFF1;
  border-radius: 10.2em 0 0 10.2em;
  top: -0.1em;
  left: -0.1em;
  -webkit-transform-origin: 5.2em 5.1em;
  transform-origin: 5.2em 5.1em;
  -webkit-animation: load2 2s infinite ease 1.5s;
  animation: load2 2s infinite ease 1.5s;
}

.loader:after {
  width: 5.2em;
  height: 10.2em;
  background: #E9EFF1;
  border-radius: 0 10.2em 10.2em 0;
  top: -0.1em;
  left: 5.1em;
  -webkit-transform-origin: 0px 5.1em;
  transform-origin: 0px 5.1em;
  -webkit-animation: load2 2s infinite ease;
  animation: load2 2s infinite ease;
}

@-webkit-keyframes load2 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@keyframes load2 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
~~~

### Get tasks from Database
{: .dotted .white-dots}

Start by installing the `axios` module `npm install axios --save`.

For all our calls to the database we will create our own instance of `axios`. In our `utils` folder add the file `axios-tasks.js`, from here we will export the new axios instance with the `baseURL` configured to our database url. Once the file is created import it into the `Tasks` component.

~~~
import axios from "axios";
import { DB_BASE_URL } from "./constants";

const instance = axios.create({
    baseURL: DB_BASE_URL
})

export default instance;
~~~

We need to wait for `<Tasks />` to mount before we can request our tasks from the database so add the `componentDidMount()` lifecylce method and from here we will make our initial data request. We want to get the ten latest tasks so when we request the tasks from the database we will need to pass along some parameteres, `orderBy` and `limitToLast`. `orderBy` will sort the `createdAt` values in ascending order and `limitToLast` will get the last 10. The data returned will be an object which we will turn into an array so we can reverse the order of the tasks before we set the tasks in our state.

Create the function `initDataLoad()` outside of the class and call it in componentDidMount

~~~
function initDataLoad(){
  return axios.get('tasks.json', {
        params: {
            orderBy: '"createdAt"',
            limitToLast: 10
        }
    })
}
~~~ 

~~~
 componentDidMount(){
        initDataLoad().then(({data:tasks})=>{    
            const tasksArray = Object.entries(tasks).reverse()        
            this.setState({ tasks: tasksArray} ) 
        })
    }  
~~~

`state.tasks` will now be populated after `<Tasks />` has been mounted so we will initialise `state.tasks` to `null` and can remove the `getStatus()` function and `TaskStatus` import from our `Tasks` component.

~~~
state = {
        selectedTask: null,
        tasks: null
    }
~~~

After the tasks are set `state.tasks` will be an array of arrays where each array has two values, an id and the task data, this is different to our original data structure so we must update the logic used to generate of our `<TaskCard />`'s in `TasksLeft`. While we have no `<TaskCard>`'s we will show the `<Spinner />`

~~~
// ./components/TasksLeft/TasksLeft.js

// ...
import Spinner from '../UI/Spinner/Spinner';

const TasksLeft = ({ tasks, visible, width }) => {
    let taskCards = null;
    
    if (tasks) {
        taskCards = tasks.map(([id, task]) => {
                task.id = id;
                return <TaskCard key={id} task={task} />
        })        
    }

    const display = visible ? {} : { display: 'none' } 

    return (<Grid item xs={width} className="i-scroll tlc-width" style={display}>
        {taskCards || <Spinner /> }
    </Grid> );
}
 
export default TasksLeft;
~~~

### Load more tasks
{: .dotted .white-dots}

When we get to the near the bottom of our list of `<TaskCard />`'s we want to load the next ten oldest tasks and keep doing so until there are no more to load. We will render a new component in `TasksLeft` that will, once it comes into the viewport, make a request for the next lot of tasks.

First Let's work on updating `Tasks`, we will add the following properties to `state`: `hasMore`, `isFetching`, `taskIds`. `hasMore` will indicate if there are possibly more tasks to retrieve from the database, `isFetching` indicates if we are currently fetching more tasks from the database, used to prevent sending concurrent requests, and `taskIds` will contain all the task ids of retrieved tasks. 

~~~
state:{
	hasMore: true,
	isFetching: false,
	taskIds: []
	// ...
}
~~~

Next we need a `loadMoreTasks()` function that we can call to retrieve more tasks if they are available, if no more are available we will set `hasMore` to false.

~~~
loadMoreTasks = () => {
        const { hasMore, isFetching, tasks } = this.state;

        if (hasMore && !isFetching) {
            this.setState({isFetching: true});

            axios.get('tasks.json',{
                params:{
                    orderBy:'"createdAt"',
                    limitToLast: tasksToFetch + 1,
                    endAt: tasks[tasks.length -1][1].createdAt
                }
            }).then(({data:tasks}) => {
                const tasksArray = Object.entries(tasks).reverse();
                tasksArray.shift()
                const newTaskIds = tasksArray.map(([id]) => id)
                
                if (tasksArray.length > 0) {
                    this.setState(({tasks, taskIds}) => {
                        // possibly more tasks available if equal
                        const hasMore = tasksArray.length === tasksToFetch; 
                        
                        return {
                            isFetching: false,
                            tasks:[...tasks, ...tasksArray],
                            taskIds: [...taskIds, ...newTaskIds ],
                            hasMore
                        }
                    })
                }else{
                    this.setState({ hasMore: false, isFetching: false })                    
                }
            })
        }
    }
~~~

We will soon be needing to use `loadMoreTasks` in `TasksLeft` so we will go ahead and pass it into `<TasksLeft />` ready to use in the next step.

`<TasksLeft ... loadMore={this.loadMoreTasks}/>`

Next we need to make the component that we can watch, we will use the [scrollmonitor-react][scrollmonitor-react]{: .white .white-hover-dots rel="nofollow"} library and the `Watch` and `ScrollContainer` HOC's to implement this.

Add the `scrollmonitor-react` package to our app `npm install scrollmonitor-react --save`

In our `UI` folder add a new component `Watched`, this will be a functional component that returns an empty `span` and is then passed to the `Watch` HOC.

~~~
// UI/Watched/Watched.js

import React from 'react';
import { Watch } from 'scrollmonitor-react';

export default Watch(() => <span></span>);

~~~

Next we need to import `ScollContainer` HOC from `scollmonitor-react` and our `Watched` component into `TasksLeft`, update the export and add `<Watched />`. We need to set a few properties on `<Watched />`, `ScrollContainer()` is going to give us a new prop `scrollContainer` which we need to pass to `<Watched />` on it's `scrollContainer` prop, `fullyEnterViewport` takes the function that will be called everytime the watched component enters the viewport, to this we will pass the `loadMore` function the we passed in from `<Tasks />` earlier and we'll add `key` with the value `'watched'`. 

~~~
// tasksleft

// ...
import { ScrollContainer } from "scrollmonitor-react";
import Watched from "../UI/Watched/Watched";

const TasksLeft = ({ tasks, visible, width, scrollContainer, loadMore }) => {
    let taskCards = null;
    
    if (tasks) {
        taskCards = tasks.map(([id, task]) => {
                task.id = id;
                return <TaskCard key={id} task={task} />
        })
        
        taskCards.splice(-2,0,<Watched 
            key={'watched'}
            scrollContainer={scrollContainer}
            fullyEnterViewport={loadMore}
        />)
    }

    const display = visible ? {} : { display: 'none' } 

    return (<Grid item xs={width} className="i-scroll tlc-width" style={display}>
        {taskCards || <Spinner /> }
    </Grid> );
}
 
export default ScrollContainer(TasksLeft);

~~~

Instead of outputting `taskCards` and `<Watched />` independently we only want `<Watched />` rendered if there are `taskCards` so we will splice `<Watched />` into the `taskCards` array before the last two cards, this way we will also begin loading the next lot of tasks before we've scrolled to the bottom, giving us infinite scrolling. As `<Watched />` is now rendered as part of an array this is why we gave it a key.

### Selecting a task
{: .dotted .white-dots}

At this point if we start up the development server `npm run start` and view our app we will see our initial tasks loaded on the left and as we scroll down we will get the next lot of tasks loaded from the database, awesome!!!. Now click on a task...not awesome, as the `state.tasks` data structure was changed we need to add a line and change two lines in `componentDidUpdate()` in `Tasks` to get task selection working again. We will now find the position of `taskId` in `taskIds` and then use that position to select the task from `tasks`.

Before we can access `taskIds` we will need to update `initDataLoad` in `componentDidMount()` to also set our initial `taskIds`.

~~~
componentDidMount(){
        initDataLoad().then(({data:tasks}) => {
            const  tasksArray = Object.entries(tasks).reverse();
            const newTaskIds = tasksArray.map(([id]) => id);
            this.setState({ tasks: tasksArray, taskIds: newTaskIds})
        })
    }
~~~

~~~
 componentDidUpdate(prevProps) {
        const { taskId: previousId } = prevProps.match.params;
        const { taskId } = this.props.match.params;

        if (previousId !== taskId) {  

            let selectedTask = null;

            if (taskId) {
                const { tasks, taskIds } = this.state;
                let taskIndex = taskIds.findIndex(id => id === taskId);
                selectedTask = tasks[taskIndex][1]; 
            }

            this.setState({ selectedTask })
        }
    }
~~~

Now that we can again select tasks from within `<TasksLeft>` there is one more issue to resolve, if we select a task and refresh the page we aren't shown the task even though there is a `taskId` in the url. Currently our selected task is set when the `<Tasks />` updates, we need to now set it up so that it is also set when the component did mount so let's update `componentDidMount()`.

We can't wait for the initial loading of tasks and then select the task as there is no guarantee that the task we are after is one of the ten latest tasks instead we will solve this so that when `<Tasks />` mounts we do our `initDataLoad` and if a `taskId` is provided we will also load the task.

Add a `loadTask()` function to our `Tasks` file which takes one parameter, `taskId`, and requests that task from the database. 

~~~
// ./containers/Tasks/Tasks.js

// ...

function loadTask(taskId) {
    return axios.get(`tasks/${taskId}.json`)
}

class Tasks extends Component {

	// ...

}

export default Tasks;
~~~

now update `componentDidUpdate()`. We will do our `initDataLoad()` and if there is a `taskId` we will do a `loadTask()` and then wait for all responses before we do `this.setState()`.

~~~
componentDidMount() {

        const promises = [initDataLoad()];
        const { taskId } = this.props.match.params;
      
        if (taskId) {
            promises.push(loadTask(taskId))
        }

        Promise.all(promises).then((responses) => {
            const { data: tasks } = responses[0];
            const tasksArray = Object.entries(tasks).reverse();
            const taskIds = tasksArray.map(([id]) => id)       
            const newState = { tasks: tasksArray, taskIds };

            if (responses.length === 2) {                
                newState.selectedTask = responses[1].data
            }

            this.setState(newState);
        })
    }
~~~

To demonstrate this is working we can scoll down and select a task after the 10 from the initial data load, reload the page and we will see that it is displayed even though it is not in the data from the `initDataLoad()` function. 

### The End
{: .dotted .white-dots}

Now that the app is looking better on the task front in the next tutorial in the series we will update the components to use all the new task data fields and update the stying to better match the design of Airtasker.

[css-spinner]: https://projects.lukehaas.me/css-loaders/
[scrollmonitor-react]: https://github.com/stutrek/scrollmonitor-react


