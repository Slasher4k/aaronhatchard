---
layout: react
title: "Fairtasker: Part 2"
metadescription: "Create an Airtasker clone with React part 2. Update styling and add new functionality."
---

{::options auto_ids="false" /}

#### (Part 2)

Hello, I see you're excited to continue the development of Fairtasker, the Airtasker clone. Today we are going to build off what we did in [part one][part-one]{: .white .white-hover-dots rel="nofollow"}, we'll start with the sytle improvements and add spacing around componenents and put a coloured stripe on the left of the task cards to indicate task state. On the functionality front we are going enable the selecting of tasks from the list of tasks, instead of relying on our timer, and add the abilty to switch the layout based on the screen size. 

Knowledge requirements:

* Javascript (ES6)
* React
{: style="list-style: inside;"}

## Styling Updates
{: .dotted .white-dots}

The first thing we will do is visit our main CSS file `index.css`, add the `html` selector and assign a background color of `#E9EFF1`{: .language-none}.

~~~
/* ./index.css */

html{
  background-color: #E9EFF1;
}
~~~
{: .language-css}

Next we want to improve the look our `<TaskCard />`'s, their styling will be based on a task's status which can be one of three values: `"Open"`, `"Assigned"`, `"Completed"`. The task status will determine the colour of the left hand side border of the `<TaskCard />`. We will create a `constants.js` file in a `utils` folder that will export a `TASK_STATUS` object providing those values.

~~~
// ./utils/constants.js

export const TASK_STATUS = {
    ASSIGNED: "Assigned",
    COMPLETED: "Completed",
    OPEN: "Open",
}
~~~

These task status values will be used for a few things, firstly they will be used to the create the `<TaskCard>` CSS selectors and later used as the value for the `className` prop on each `<TaskCard />`.

To apply our new CSS we will use the `withStyles()` function from `material-ui` in `TaskCard.js`. `withStyles()` takes one argument, an object which defines our CSS and returns a HOC. In the end our component will have a new prop passed to it called `classes` which contains an object like: `{root: "TaskCard-root-174"}`, where the key is the DOM element within the `material-ui` component's native DOM elements that the class `"TaskCard-root-174"` will be applied to.

To learn more about customising `material-ui` component styles [read the official documentation][class-naming]{: .white .white-hover-dots rel="nofollow"}.

~~~
// ./components/Tasksleft/TaskCard/TaskCard.js

// ...

import { withStyles } from '@material-ui/core/styles';

const styles = {
    root: {
        borderLeft: "3px solid",
        margin: '10px 0',
        '&:first-of-type': {
            marginTop: 0
        },
        '&:last-of-type': {
            marginBottom: 0
        },
        [`&.${TASK_STATUS.ASSIGNED}`]: {
            borderLeftColor: '#f4be36'
        },
        [`&.${TASK_STATUS.COMPLETED}`]: {
            borderLeftColor: '#1339c5'
        },
        [`&.${TASK_STATUS.OPEN}`]: {
            borderLeftColor: 'limegreen'
        },
    }
}

const TaskCard = ({ task, classes }) => {
   // ...
}
 
export default withStyles(styles)(TaskCard);

~~~

To show the diffenent colours based on task status go to `Tasks.js` and import our `TASK_STATUS` constant object from `constants.js` and then create the function `getStatus()` which will take a number as it's parameter and return one of the three available status values. In the anonymous function passed to `map()` we will use the remainder of the array index divided by three passed to  `getStatus()` to determine a task's status.

~~~
// ./containers/Tasks/Tasks.js

// ...
import { TASK_STATUS } from '../../utils/constants';

const getStatus = (status) => {
    switch (status) {
        case 0: return TASK_STATUS.OPEN;                
        case 1: return TASK_STATUS.ASSIGNED;           
        default: return TASK_STATUS.COMPLETED
    }
}

class Tasks extends Component {
    state = {
        selectedTask: null,
        tasks: Array(8).fill(0).map( (_, i) => ({
            id: '' + i,
            title: `Task ${i}`,
            description: `I am task ${i}`,
            status: getStatus(i % 3) })) 
    }
// ...
 
export default Tasks;
~~~

Now for the independant scrolling of `<TasksLeft />` and `<TasksRight />` add the following CSS to `index.css`.

~~~
.i-scroll {
  height: calc(100vh - 64px); /* Subtract <AppBar /> height */
  overflow-y: auto;
}
~~~
{: .language-css}

Add `i-scroll` to the `<Grid />`'s `className` prop in `TasksLeft.js` and `TasksRight.js`.

~~~
// ./components/TasksLeft/TasksLeft.js

// ...

    return (<Grid item xs={3} className='i-scroll'>{cards}</Grid> );

// ...
~~~

~~~
// ./components/TasksRight/TasksRight.js

// ...

    return (<Grid item xs={4} className='i-scroll'>//...</Grid>);

// ... 
~~~

Now to restrict the minimum width of `<TasksLeft />` we will add another class, `tlc-width`, to go along with `i-scroll`.

~~~
.tlc-width {
  min-width:300px;
}
~~~
{: .language-css}

~~~
// ./components/TasksLeft/TasksLeft.js

// ...

    return (<Grid item xs={3} className='i-scroll tlc-width'>{cards}</Grid> );

// ... 
~~~

One last thing, go to `Layout.js` and add the prop `color` to the `<AppBar />` and set it to `"default"`

~~~
<AppBar position="static" color="default">
// ...
</AppBar>
~~~


## Functionality Updates
{: .dotted .white-dots}

The first piece of functionality we are going to work on is removing the timer from `render()` in `Tasks.js` and being able to select a task via a `<TaskCard />`. To implement this we will using routing provided by `react-router-dom`.

Delete the `if(!selectedTask)` statement from `render()`. The selected task is going to be chosen based on a segment from the url, our url will take the structure `<domain>/<taskId>` eg `http://localhost:3000/4`{: .language-none} has the task id `4`{: .language-none}.

To use `react-router-dom` components we must import the `BrowserRouter` component into `./index.js`  and nest `<App />` as it's child.

~~~
// ./index.js
// ... 
import { BrowserRouter } from "react-router-dom"

ReactDOM.render(<BrowserRouter><App /></BrowserRouter>, document.getElementById('root'));
~~~

Next we will go to `TaskCard.js` and wrap the `<CardContent />` in a `<Link />` and point to the task id.

{% assign style1 = "{{ root: classes.root }}" %}
{% assign style2 = "{{textDecoration: 'none', color: 'white' }}" %}

~~~
// ./components/Tasksleft/TaskCard/TaskCard.js

// ...
import { Link } from 'react-router-dom';

    return ( <Card classes={{ style1 }} className={task.status}>
        <Link to={task.id} style={{ style1 }}>
            <CardContent>
                 // ...
            </CardContent>
        </Link>
        </Card>
     );
}
 
export default withStyles(styles)(TaskCard);

~~~

The CSS for our `a` tag has some unwanted underlining so we will add some CSS to `index.css` to remove it.

~~~
a {
    text-decoration: none;   
  }
~~~
{: .language-css}

Now if you click on a `<TaskCard />` you will see the url change to include the task id.

To access the `taskId` we will update `App.js` to use `<Route />` and have it render `Tasks` as this will then provide us with the `taskId` from the url. We'll use a `<Route />` with path `/:taskId?` (the ? indicates the parameter is optional), we will then check in `Tasks` for the `taskId` value. 

~~~
// ./App.js

// ...

import { Route } from 'react-router-dom';

class App extends Component {
  render() {
    return (<Layout>
              <Route path="/:taskId?" component={Tasks} />              
            </Layout>);
  }
}

export default App;
~~~

The `<Route />` will pass a `match` prop to `<Tasks />` and we will check it's `params` property for a `taskId`, as this prop will change with each different url we will use the component's `componentDidUpdate()` method to check if the value of `taskId` changes, if it does we will update our `selectedTask` .

~~~
// ./containers/Tasks/Tasks.js

// ...

    componentDidUpdate(prevProps){
        const { taskId: previousId } = prevProps.match.params;
        const { taskId } = this.props.match.params;

        if (previousId !== taskId){

            let selectedTask = null;
            
            if (taskId) {
                selectedTask = this.state.tasks[taskId]
            }

            this.setState({ selectedTask })
        }        
    }

// ...

export default Tasks;
~~~

We can now select different tasks and have their details displayed in `<TaskDetails />`.

To be able to navigate back to an unselected task state we will add a link to `"Fairtasker"` in the navigation bar to take us to the home page. We will do this by simply importing `Link` and use it as the component used by `<Typography />`, as it will now render as a `<Link />` we must still add our `to` prop.

{% assign style3 = "{{ padding: '8px' }}" %}

~~~
// ./components/Layout/Layout.js

// ...
import { Link } from 'react-router-dom';

const Layout = ({ children }) => {
    return (<><AppBar position="static">
                    <Toolbar>
                        <Typography variant="h6" component={Link} to='/' >
                            Fairtakser
                        </Typography>
                    </Toolbar>
                </AppBar>
                <div style={{ style3 }}>                
                    {children}           
                </div>
            </>)
}
 
export default Layout;
~~~

The next thing we need to work on is changing the layout for mobile to only show `<TasksLeft />` or `<TasksRight />`. The `react-responsive` module is going to allow use to do this using a `<MediaQuery />` component and a `maxWidth` prop to determine when to change the layout.

`<MediaQuery />` takes a function as a child which takes a `boolean` parameter to indicate if our `maxWidth` property is matched, from this function we return the components that we want to render.
~~~
// ./containers/Tasks/Tasks.js

// ..
import MediaQuery from 'react-responsive';

class Tasks extends Component {
   
   // ...

    render() { 
        const { tasks, selectedTask} = this.state;        

        return ( <Grid container spacing={16} justify="center">
                    <MediaQuery maxWidth={880}>
                        {(matches) =>{
                            let leftVisible =  true;
                            let rightVisible = true;
                            let leftWidth = 3;
                            let rightWidth = 5;
                            
                            if (matches) {
                                rightVisible = selectedTask !== null;
                                leftVisible = !rightVisible;
                                leftWidth = 6;
                                rightWidth = 10;
                            }                        

                            return  <>
                                <TasksLeft tasks={tasks} visible={leftVisible} width={leftWidth}/>
                                <TasksRight task={selectedTask} visible={rightVisible} width={rightWidth}/>
                            </>
                        } }
                    </MediaQuery>
                </Grid>);
    }}
 
export default Tasks;
~~~


We created two variables `lefttVisible` and `rightVisible` to indicate which task column to show,  if our screen is less than 881px `matches` is `true` and we decide whether to show `<TasksLeft />` or `<TasksRight />` depending on if there is a `selectedTask`. Once we have final visiblity values we then pass them to `<TasksLeft />` and `<TasksRight />` via the new `visible` prop.

While we're adding new props, we also want to adjust the width of `<TasksLeft />` and `<TasksRight />` on smaller screens so we'll use the variables `leftWidth` and `rightWidth` and pass them in via the new `width` prop.

Now that we have new props on `<TasksLeft />` and `<TasksRight />` we need to use them. We will add inline styles to hide the component if `visible` is `false`, you could also do it by adding another class name and adding accompanying styles to `index.css`. We'll pass on the new `width` prop directly to the `<Grid />`'s `xs` prop.

~~~
// ./components/TasksLeft/TasksLeft.js

// ...

const TasksLeft = ({ tasks, visible, width }) => {
    const taskCards = tasks.map(task => <TaskCard key={task.id} task={task} />)
    const display = visible ? {} : { display: 'none' } 

    return (<Grid item xs={width} className="i-scroll tlc-width" style={display} >{taskCards}</Grid> );
}
 
export default TasksLeft;
~~~

~~~
// ./components/TasksRight/TasksRight.js

// ...

const TasksRight = ({ task, visible, width }) => {
    const display = visible ? {} : { display: 'none' } 

    return (<Grid item xs={width} className='i-scroll' style={display}>
        { task ? <TaskDetails task={task} /> : <TasksMap /> }         
    </Grid> );
}
 
export default TasksRight;
~~~

If the development server isn't already running start it with `npm run start` and view the current state of our app, it will be looking a bit better than where we left it in [part one][part-one]{: .white .white-hover-dots rel="nofollow"}:

[![Fairtasker after style updates][final-image]{: .u-responsive-img}][final-image]

And if we view the app on smaller screens we will only see `<TasksLeft />` or `<TasksRight />` when we select a task.

[![Fairtasker on mobile gif][mobile-gif]{: .u-responsive-img style="margin: 0 auto;"}][mobile-gif]

### The End
{: .dotted .white-dots}

Alright, alright, alright, things are starting to look pretty good, if I do say so myself. We did a lot of work and it is definitely starting to mimic Airtasker so we're on the right track but still have a bit of work to do. It is not useful for the future of our app to be creating the same tasks everytime we load the page, we need to persist and load them from some type of permanent storage. In part 3 we are going to setup up a database using [firebase][firebase]{: .white .white-hover-dots rel="nofollow"}, seed it with some tasks and then load tasks from the database once the page loads. 

[part-one]: https://www.aaronhatchard.com.au/2019/02/22/react-fairtasker.html
[class-naming]: https://material-ui.com/customization/overrides/#overriding-with-classes
[firebase]: https://firebase.google.com/
[final-image]: \images\fairtasker-part-two-final-1.png
[mobile-gif]: \images\fairtasker-mobile.gif