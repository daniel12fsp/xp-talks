<!---   HEADER  -->
### XP - Less is More
#### Alexandre/Daniel/Jack

WillUpdateReact();

<!---   SCROLL   -->
---
## Scroll - The easy way

Do you still have focus problem?

+++
Because we probably need to scroll
```js
    element.focus();
```

```js
    const lastFocusedElement = document.activeElement;

    // Set a new focused item
    (newElement:HTMLElement).focus();

    // Listener events ( onKeyDown )
    // Do more logics
    // In the end, return focus to the old active Element
     (newElement:HTMLElement).blur();
    lastFocusedElement.focus();

```

+++
Easy, right?
```js
  verifyScrollIntoView: (el, container) => {
    if (container == null) return;
    // selected DOM element
    const selectedElement = el;
    // position of selected option relative to scroll
    const currOptionTop = selectedElement.offsetTop - container.offsetTop;
    // height of selection option
    const currOptionHeight = selectedElement.offsetHeight;
    // visible height according to number of items and size of screen
    const visibleHeight = container.offsetHeight;
    // current position of scroll bar (minimum scroll top visible)
    let scrollTop = container.scrollTop;
    // calculate position of scroll bar for head of selected element
    const scrollTopElement = currOptionTop;// - this.initialOffsetTop;
    // calculate scroll bar position in the end of visible height
    // of content (maximum scroll top visible)
    const scrollTopMaxVisible = (scrollTop + visibleHeight) - currOptionHeight;
    // verify if element is before the first visible element in content (minimum scroll top visible)
    if (scrollTopElement < scrollTop) {
      scrollTop = scrollTopElement;
      // verify if element is beyond the last visible element in content (maximum scroll top visible)
    } else if (scrollTopElement > scrollTopMaxVisible) {
      scrollTop += scrollTopElement - scrollTopMaxVisible;
    }
    // update scroll top of content scroll bar
    container.scrollTop = scrollTop;
  }

```


+++
More easy, very portable right?
```js
applyScrollControl = () => {
    const selectedElement = this.getChildSelected();
    if (selectedElement === null || this.state.index === this.state.lastIndex) return;
    if (this.state.index === this.initIndexFocusable){
      setTimeout(() => { if (this.navigator) ReactDOM.findDOMNode(this.navigator).scrollTop = 0 });
    } else if (!(this.childRef[this.state.index] && this.childRef[this.state.index].props._scrollable)) {
      setTimeout(() =>
      ScrollHelper.verifyScrollIntoView(selectedElement, ReactDOM.findDOMNode(this.navigator)));
      this.state.lastIndex = this.state.index;
    }
  };
```

+++
God, please, forgive us all! :(

```js
// Not portable
ReactDOM.findDOMNode(this.navigator)
```
```js
// Countless side-effects
setTimeout(() => scrol...)
```
```js
// Focus dependent
this.initIndexFocusable
```
```js
// What ?
this.setState({}, this.setState({}))
```


+++
![wtf](http://geradormemes.com/media/created/utg1uy.jpg)

+++
We will respect our ancestors, but..

Is there another alternative?

+++
### Yes
#### scrollIntoView (Native Hero)
```jsx
    const scrollOptions = {...};
    // Get a element and call scroll...
    element.scrollIntoView(scrollOptions);
    // Simple, right?
```

+++
### scrollIntoView Options
```js
const scrollOptions = {
    // Can be called => speed ;)
    behavior:"auto", // "instant"|"smooth"|"auto"(default)

    // Vertical axis ( Why not just vertical? )
    // "end"|"start"|"center"(default)|"nearest"
    block:"center",

    // Horizontal axis ( Why not just horizontal? )
    // "end" | "start" | "center" | "nearest" (default)
    // Sorry, don't work in our device  :(
    inline:"nearest" //

}

```

+++
#### Pure Js way
```HTML
<div class="list" style="overflow:scroll">
    <div id="a" class="item">a</div>
    <div id="b" class="item">b</div>
    <div id="c" class="item">c</div>
    <div id="d" class="item">d</div>
    <div id="f" class="item">f</div>
</div>
<script>
    const element = document.getElementById("c");
    //just scroll
    element.scrollIntoView({behavior:"smooth"});
</script>
```

+++
JSX.ref as a function

```js
onSetRef(node:HTMLElement) {
  // manipulate node (HTMLElement)
}

<div ref={onSetRef} />
```

+++
#### React way
```js

renderItem = (item, currentIndex) => {
    // Choose the selected item in your logic
    if (isSelected) onRef = scroll;
    return (<div ref={onRef}>{item}</div>);
}

renderContent() {
    // Whatever list...
    const { list } = this.state;
    return (<div style={styles.bodyList}>
        {list.map(this.renderItem)}
    </div>)}
```

+++
#### Scroll function
```js
scroll(node) {
  node.scrollIntoView({behavior:'smooth', block:'start' });
}
```


+++
#### Lessons Learned
Please, do not recreate the wheel.

We have javascript Apis.




<!---   Life Cycles   -->
---
### React Life Cycles
 ![React LifeCycle](https://rangle.github.io/react-training/img/reactjs_component_lifecycle_functions.png)

---
### Stateless/Statefull
##### Dumb/Smart

+++
#### Stateless
- You only need props.
- You don't change any state inside
- They don't have any life cycle(ComponentDidMount, ComponentWillReceiveProps).
```js
function Overlay(props){
    return (
        <div className="overlay" onClose={props.onClose}>
        {props.children}
        </div>
    );
}
```
+++
#### Statefull
- Statefull component has one or more components
- Look like "Page"
- They are can be very complex
+++
```javascript
    class Page extends Component{
        state :{
           overlayShow: true
        }
        onClose =() =>{
            this.setState({overlayShow: false})
        }
        render() {
            const {overlayShow} = this.state;
            return (
               <div className="page">
                    <Input />
                    <Overlay enable={overlayShow} onClose={this.onClose}/>
                </div>
            );
        }
    }
```

---
## Page Class 
#### Composition vs Inheritance with React

+++ 

"At Facebook, we use React in thousands of components, and we haven’t found any use cases where we would recommend creating component inheritance hierarchies."

+++

(Someone told me)

```js
class Profile extends Page<Props, {}> {
    
    renderTitle() {
        return 'TOOO';
    }

    renderContent() {
        return <div>Profile</div>;
    }
}
```

+++

### Use carefully
 <div class="fragment">- To propagate pattern</div>
 <div class="fragment">- To abstract common behaviors</div>
 <div class="fragment">- To avoid repetition</div>

+++ 
#### Think in this case...

```js
class PageA extends Component {
    state = {
        header:"Page A header",
        keys:this.createSoftKeys()
    }
    createSoftKeys() {}
}

class PageB extends Component {
    state = {
        myHeader:"Page B header",
        softKeys:this.my_softKeys()
    }
     my_softKeys() {}
}

class PageC extends Component {
    state = {
        title:"Page B header",
        sKeys:{Enter:..., left:"left"}
    }  
}
```

+++

### All pages did the same thing, but differently...
 Inheritance, in this case, can be a weapon.

+++ 

The "header" information and "softKey" are common, so lets create a Page class
```javascript
export default class Page extends Component {
  computeHeader() { return <Header textL10nId={this.state.titleId} />; }
  computeSoftkeys() { ERROR`You don't declared computeSoftkeys`; }
  renderContent() { ERROR`You don't declared renderContent`; }

  render() {
    return (
      <div className="page flex-column">
        {this.computeHeader()}
        <div className="content">{this.renderContent()}</div>
        <Softkey {...this.computeSoftkeys()} />
      </div>
    );
  }
}
```

+++
#### Header normalized in a common state

```js
export default class PageA extends Page {
  state = {
    titleId: 'My Header'
  };

  computeSoftkeys() { ...  }

  renderContent() {
    return <div> Simple Page with Header and Softkey</div>;
  }
}

```

+++
#### SoftKey is also in common.

```js
export default class PageA extends Page {
  ...
  computeSoftkeys() { 
      return {
          left:"Cancel",
          center:"Ok",
          right:"Options",
          SoftLeft: ()=>{LOG`Soft Left called!`},
          Enter: this.myFn,
          SoftRight: ()=>{LOG`Right Left called!`},         
      }
    }
  myFn = () => {};
  ...
}

```




---
<!---   Boolean   -->

# Boolean 101
+++
## True values

<div class="fragment">
- `true`
</div>


<div class="fragment">
-  `It's new instance(new Object())`
</div>

<div class="fragment">
-  any number !=== 0
</div>

+++

## False values

- `false`
- `""`
- `0`
- `undefined`
- `null`

+++
###  Short circuits evaluations

+++

#### And
```javascript
    // Short circuits with and
    variable && callFunction()
    //equal
    if (variable){
        callFunction();
    }
```
#### You can use in react
```javascript
class Page extends Component{
    ...
    render() {
        const {overlayShow} = this.state;
        return (
            <div className="overlay">
                {overlayShow && <Overlay/>}
            </div>
        );
    }

}
```
+++

#### Or

##### You can use for initializer variables
```javascript
    const window = window || {};
```
+++
### Short circuits evaluations - what's the value?

```js
const items =[0,1,null,3];
const values =  items.map(item => {
    if (item){
        return item;
    } else {
        return 'invalid';
    }
});
console.log(values)
```

##### What will be the answer?

- a) [ 'invalid', 1, 'invalid', 3 ]
- b) [ 0, 1, 'invalid', 3 ]
- c) ['invalid', 1, null, 3 ]

---
<!---   ES6   -->

# ES6

+++

## Map
```js
const array = [1,2,3];
console.log(array.map((x) => x**2))
//[1,4,9]
```
###  in React

```js
renderContent() {
    // Whatever list...
    const { list } = this.state;
    return (<div style={styles.bodyList}>
        {list.map(this.renderItem)}
    </div>)}
```
+++
## Filter
```js
const array = [1,2,3];
console.log(array.filter((x) => x % 2!==0))
//[1,3]
```
+++
## Includes
```js
const array = [1,2,3];
console.log(array.includes(4))
//false
console.log(array.includes(2))
//true
```
+++
## Set
```js
const set =  new  Set([1,2,3])
set.add(4);
//Set([1,2,3,4])
set.has(4);
//true
set.delete(4)
//4
set.size()
//length: 3

```
+++
## Template literal
```js
const mDate = `${day}/${month}/${year}`;
```
+++

## 1 - Destructuring
```js
// Instead of
// :(
function Component(props){
    return <div> {props.name}, {props.age}</div>;
}


// Use this
// :)
// Destructor parameter
function Component({name, age}){
    return <div> {name}, {age}</div>;
}

```
+++
## 2- Destructuring


```js
class Page extends Component{
    ...
    render(){
        //Destructor assignment
        const {title,  ...otherProps} = this.props;
        const {name,age} = this.state;
           console.log(otherProps);
        return `${name}, ${age}`
    }
}
```

---
<!---   Keep render simple   -->

### Keep render simple
- Destructors
- Secondary renders
- Avoid arrow functions in props
---
### (If dev/prod)
<!---   If dev/prod   -->


#### For production
```bash
    webpack -p
    NODE_ENV=production webpack

```

```js
  if (process.env.NODE_ENV !== 'production') {
      //code not bundle
  }
```

---
### New Deploy scripts
A command line tool for b2g devices  
Author: -> Fabrice Desré  (https://github.com/fabricedesre)
![Fabrice](https://avatars0.githubusercontent.com/u/984767?s=460&v=4)




+++

`app:install`  
Install the app on the current attached device  
***     
`app:uninstall`  
Uninstall the app   
***     
`app:start`  
Start the app on the device (if installed)  
***         
`app:stop`            
Stop the App and return to the launcher  
***
`app:update`           
Update the current app  

---
### CSS Module

##### Why should we use CSS Modules?
CSS Modules works with scoped styles.

1. Live in one place
2. Only apply to that component and nothing else

+++

##### The key benefit
Write CSS as quickly as possible without considering what else you might affect.

```js
//before
class ListLine {
...
render (){
    return (
    <div className="kai-list-line__containerText">
        {renderLabel(props)}
    </div>
    );
    }
}
```
+++
##### The key benefit

```js
//after
import styles from './ListLine.css';
class ListLine {
...
render (){
    return (
    <div className={styles.containerText}>
        {renderLabel(props)}
    </div>);
    }
}
```

```css
.containerText {
  width: 100%;
  height: 5.4rem;
  display: flex;
}
```

+++
#### Generated HTML and CSS
```html
<div class="ListLine__listLine___2nV7d focusable" tabindex="0">
  <div class="ListLine__containerText___23SmD">
    <div class="ListLine__textEllipsis___ALF1F ListLine__text___Sx">
      <label class="ListLine__text___Sx8_V">Sample Text</label>
    </div>
  </div>
</div>
```

```css
.ListLine__containerText___23SmD {
    width: 100%;
    height: 5.4rem;
    display: flex;
    align-items: center;
    white-space: nowrap;
    overflow: hidden;
    margin: 0 1rem;
}

.ListLine__containerChildren___Eyatw {
    padding-right: 1rem;
}
```
+++
##### Composes keyword
```css
.text {
    color: var(--color-gs90);
}
.textEllipsis {
    composes: text;
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}

```
When used like
```jsx
const jsx =  <div className={styles.textEllipsis}></div>
```
Will generate:
```html
<div class="ListLine__text___Sx8 ListLine__textEllipsis___ALF1">
</div>
```
+++
##### Compose from another file
```css
.header {
    composes: colorYellow from './frontend/Theme.module.css';
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}
```

+++

#### Please, don't do this =(
```js
const _class  =
  classnames('kai-date-picker__modal--modal-content--modal-body 
  kai-overlay-content kai-date-picker__with-softkey')
const jsx = (<div className={_class} />);
```
---
## Intermediate
---
# Page List 101
+++
##### Method buildList()
- Add items at UI

```js
 buildList() {
    this.addItem({ type: 'first' });
    this.addItem({ type: 'second' });
    Array.from(Array(50).keys()).forEach((text, index) =>
      this.addItem({
        type: 'dynamic',
        id: index
      })
    );
  }

```
+++
##### Method computeSoftkeys(current)
- Change value of softkey and action from keyboards
```js
  computeSoftkeys(current) {
    let handlers;
    switch (current.type) {
      case 'first':
        handlers = {
          left: 'back',
          center: 'select',
          right: 'toast',
          SoftRight: () => this.props.toast('Simple toast message')
        };
        break;
      case 'dynamic':
        handlers = {
          center: 'ok',
          Enter: () => this.props.toast('Call softkey centerCallBack')
        };
        break;
    }
    return {
      ...handlers,
      ArrowUp: this.selectPrevious,
      ArrowDown: this.selectNext
    };
  }
```
+++

##### renderItem(item)
```js
  renderItem = item => {
    let itemText;
    switch (item.type) {
      case 'first':
        itemText = 'First';
        break;
      case 'dynamic':
        itemText = item.id.toString();
        break;
      default:
        Error(`You forgot to declare this item ${item.type}`);
    }
    return <ListLine text={itemText} />;
  };
```
+++

##### renderContent()
```js
  renderContent() {
    return (
        <div className="pageListContent">
            {this.renderList()}
        </div>
    );
  }
```

+++
### One source of true
- Only source of event and label
- Helps to keep simple
- Never more concurrency =)

---
### Don't use React.clone...
- It's not compatible with react like library(Inferno, Preact ...).
- React.cloneElement obfuscates how components work in parent.

---
### Next Time ...
- Flow
- Redux
- Switch as Router
---
### XP - Alexandre/Daniel/Jack
DidUpdateReact();