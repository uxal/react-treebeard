# uxal-react-treebeard

[![Build Status](https://travis-ci.org/alexcurtis/react-treebeard.svg?branch=master)](https://travis-ci.org/alexcurtis/react-treebeard) [![Coverage Status](https://coveralls.io/repos/alexcurtis/react-treebeard/badge.svg?branch=master&service=github)](https://coveralls.io/github/alexcurtis/react-treebeard?branch=master)

React Tree View Component. Data-Driven, Fast, Efficient and Customisable.

### Install

```
npm install uxal-react-treebeard --save
```

### UXAL description (after initial fork)

This is a fork of the [react-treebeard](https://github.com/alexcurtis/react-treebeard) project. I've forked it in order to add a new click event handler which I needed for a custom container decorator. In my scenario I wanted to be able to click on the arrows to expand/collapse the tree and do something else when clicking on the node's text.

The new click handler is called `onCustomContainerClick`

See the example below of the custom conatiner decorator
```javascript

decorators.Container = (props) => {
      const {
        style, decorators, terminal, onClick, node, onCustomContainerClick,
      } = props;
      return (
        <div className={node.active ? `${styles.treeItem} ${styles.itemSelected}` : styles.treeItem}>
          {node.children ?
            <span
              className={styles.treeToggleContainer}
              onClick={props.onClick}
            >
              <decorators.Toggle style={style.toggle} />
            </span>
            :
            <span />
          }
          <span
            className={styles.treeHeaderContainer}
            onClick={props.onCustomContainerClick}
          >
            <decorators.Header
              node={node}
              style={style.header}
            />
          </span>
        </div >
      );
    };
```

And the initialization is:
```javascript
 <Treebeard
    data={this.state.data}
    style={treeStyles}
    onToggle={this.onToggle}
    onCustomContainerClick={this.onNodeSelect}
/>
```

### [Example](http://alexcurtis.github.io/react-treebeard/)

An online example from the `/example` directory can be found here: [Here](http://alexcurtis.github.io/react-treebeard/)

### Quick Start
```javascript
'use strict';

import React from 'react';
import ReactDOM from 'react-dom';
import {Treebeard} from 'react-treebeard';

const data = {
    name: 'root',
    toggled: true,
    children: [
        {
            name: 'parent',
            children: [
                { name: 'child1' },
                { name: 'child2' }
            ]
        },
        {
            name: 'loading parent',
            loading: true,
            children: []
        },
        {
            name: 'parent',
            children: [
                {
                    name: 'nested parent',
                    children: [
                        { name: 'nested child 1' },
                        { name: 'nested child 2' }
                    ]
                }
            ]
        }
    ]
};

class TreeExample extends React.Component {
    constructor(props){
        super(props);
        this.state = {};
        this.onToggle = this.onToggle.bind(this);
    }
    onToggle(node, toggled){
        if(this.state.cursor){this.state.cursor.active = false;}
        node.active = true;
        if(node.children){ node.toggled = toggled; }
        this.setState({ cursor: node });
    }
    render(){
        return (
            <Treebeard
                data={data}
                onToggle={this.onToggle}
            />
        );
    }
}

const content = document.getElementById('content');
ReactDOM.render(<TreeExample/>, content);
```

### Prop Values

#### data
`PropTypes.oneOfType([PropTypes.object,PropTypes.array]).isRequired`

Data that drives the tree view. State-driven effects can be built by manipulating the attributes in this object. Also supports an array for multiple nodes at the root level. An example can be found in `example/data.js`

#### onToggle
`PropTypes.func`

Callback function when a node is toggled / clicked. Passes 2 attributes: the data node and it's toggled boolean state.

#### style
`PropTypes.object`

Sets the treeview styling. Defaults to `src/themes/default`.

#### animations
`PropTypes.oneOfType([PropTypes.object, PropTypes.bool])`

Sets the treeview animations. Set to `false` if you want to turn off animations. See [velocity-react](https://github.com/twitter-fabric/velocity-react) for more details. Defaults to `src/themes/animations`.

#### decorators
`PropTypes.object`

Decorates the treeview. Here you can use your own Container, Header, Toggle and Loading components. Defaults to `src/decorators`. See example below:

```javascript
const decorators = {
    Loading: (props) => {
        return (
            <div style={props.style}>
                loading...
            </div>
        );
    },
    Toggle: (props) => {
        return (
            <div style={props.style}>
                <svg height={props.height} width={props.width}>
                    // Vector Toggle Here
                </svg>
            </div>
        );
    },
    Header: (props) => {
        return (
            <div style={props.style}>
                {props.node.name}
            </div>
        );
    },
    Container: (props) => {
        return (
            <div onClick={this.props.onClick}>
                // Hide Toggle When Terminal Here
                <this.props.decorators.Toggle/>
                <this.props.decorators.Header/>
            </div>
        );
    }
};

<Treebeard data={...} decorators={decorators}/>
```

### Data Attributes

```javascript
{
    id: '[optional] string',
    name: 'string',
    children: '[optional] array',
    toggled: '[optional] boolean',
    active: '[optional] boolean',
    loading: '[optional] boolean',
    decorators: '[optional] object',
    animations: '[optional] object'
},
```
#### id
The component key. If not defined, an auto-generated index is used.

#### name
The name prop passed into the Header component.

#### children
The children attached to the node. This value populates the subtree at the specific node. Each child is built from the same basic data structure. Tip: Make this an empty array, if you want to asynchronously load a potential parent.

#### toggled
Toggled flag. Sets the visibility of a node's children. It also sets the state for the toggle decorator.

#### active
Active flag. If active, the node will be highlighted. The highlight is derived from the `node.activeLink` style object in the theme.

#### loading
Loading flag. It will populate the treeview with the loading component. Useful when asynchronously pulling the data into the treeview.

#### decorators / animations
Attach specific decorators / animations to a node. Provides the low level functionality to create visuals on a node-by-node basis. These structures are the same as the top level props, described above.
