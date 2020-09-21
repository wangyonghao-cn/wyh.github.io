---
title: REACT 井字棋游戏教程学习（二）开发井字棋游戏
---

# 编辑前的准备

> 让我们正式开始吧！

<!--more-->

> 开始前，我们要了解，react是一个js的库，通过一些代码编写成一个个组件，然后将这些组件在拼接组合成一个复杂的页面，所以我们主要编写的就是这一个个的组件
> 首先，这些组件被分成不同的类型
> 1.React.Component,这是react的子类，我们先创建一个组件类ShoppingList

``` js
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}
// 用法示例: <ShoppingList name="Mark" />
```

> 2.ShoppingList组件类可以接受一些参数，我们将这些参数叫做props,然后通过 render 方法返回需要展示在屏幕上的UI，具体来说，render方法是返回来了一个React元素

# 开始编辑

1.关注我们在第一篇文章中创建的index.js文件，通过阅读代码，我们在其中创建了三个组件类Square，Board和Game
2.Square 组件渲染了一个单独的 button, Board 组件渲染了 9 个方块。Game 组件渲染了含有默认值的一个棋盘，我们一会儿会修改这些值。到目前为止还没有可以交互的组件。

## 传递数据

1.我们先做一个测试，将Board类中传递一个数据给Square,如下

```js
//Board
    renderSquare(i) {
        return <Square value={i} />;
    }
```

2.在Square中修改render，将{ /* TODO */}替换为{ this.props.value },，这时你会看到页面中的九宫格内出现了数字

```js
//Square
    renderSquare(i) {
        return <Square value={i} />;
    }
```

3.接下来我们让格子中可以出现X，首先我们修改Square 组件，绑定一个方法

```js
//Square
    render() {
        return (<button className="square" onClick={() => { alert('click') }}> {this.props.value} </button>);
    }
```

4.接下来我们希望它可以记住它曾经被点击过，设置一个变量记录点击状态，我们用 state 来实现所谓“记忆”的功能，我们在 this.state 中存储当前每个方格（Square）的值，并且在每次方格被点击的时候改变这个值,首先初始化一个值

```js
//Square
class Square extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            value: null
        }
    }
    render() {
        return (<button className="square" onClick={() => { alert('click') }}> {this.props.value} </button>);
    }
}
```

> 5.将this.props.value替换被this.state.value即可显示state的值,将alert替换为this.setState({value: 'X'})即可绑定点击改变value的值为X,之后点击那个方格，就会出现X

```js
//Square
    render() {
        return (<button className="square" onClick={() => { this.setState({ value: 'X' }) }}> {this.state.value} </button>);
    }

```

## 状态提升

> 将每个方格的状态维护到起父组件Board中，并将其绑定到每个Square上，既修改代码为

```js
// Board
constructor(props) {
    super(props);
    this.state = {
        squares: Array(9).fill(null),
    };
}
renderSquare(i) {
    return <Square value={this.state.squares[i]} />;
}
```

> 这样我们就可以修改Board中的squares数组去控制子组件Square中的显示了，应为click事件是绑定在Square组件上的，所以我们需要想办法让Square去更新Board上的数据，由于 state 对于每个组件来说是私有的，因此我们不能直接通过 Square 来更新 Board 的 state,所以我们在Board上传进去一个函数，让Square被点击的时候被调用，如下:

```js
// Board
    renderSquare(i) {
        return (<Square value={this.state.squares[i]} onClick={this.handleClick(i)} />);
    }
```

> 接着修改Square，因我们将所有的状态数据维护在Board组件中，所以将所有state上的数据替换为传进去的value和onClick数据，代码如下：

```js
// Square
    class Square extends React.Component {
        render() {
            return (<button className="square" onClick={() => { this.props.onClick() }}> {this.props.value} </button>);
        }
    }
```

> 在Board中维护handleClick方法

```js
//Board
    handleClick(i) {
        const squares = this.state.squares.slice();
        squares[i] = 'X';
        this.setState({ squares: squares })
    }
    renderSquare(i) {
        return (<Square value={this.state.squares[i]} onClick={() => this.handleClick(i)} />);
    }
```

> 此时，实现了跟刚刚一样的效果，不过在这时，数据是维护在Board组件类中的，接下来将设置一个属性来控制每一手棋是X还是O,每次切换，代码如下

```js
//Board
    handleClick(i) {
        const squares = this.state.squares.slice();
        squares[i] = this.state.nextIsX ? 'X' : 'O';
        this.setState({ squares: squares, nextIsX: !this.state.nextIsX })
    }
```

> 在Board中检查是否有人胜利，将所有胜利的可能记录下来，当某一方所下棋子满足其中一种情况时，判断其胜利，并添加规则，若格子已有值，则不允许改变

```js
//Board
    calculateWinner(squares) {
        const lines = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8],
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8],
            [0, 4, 8],
            [2, 4, 6],
        ];
        for (let i = 0; i < lines.length; i++) {
            const [a, b, c] = lines[i];
            if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
                return squares[a];
            }
        }
        return null;
    }
    handleClick(i) {
        const squares = this.state.squares.slice();
        if (calculateWinner(squares) || squares[i]) {
            return;
        }
        squares[i] = this.state.nextIsX ? 'X' : 'O';
        this.setState({ squares: squares, nextIsX: !this.state.nextIsX })
    }
    render() {
        const winner = calculateWinner(this.state.squares);
        let status;
        if (winner) {
            status = 'Winner: ' + winner;
        } else {
            status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
        }
        ···
    }
```

> 好了，到目前为止，我们已经实现了井字棋，接下来是历史步骤的列表，这个功能需要访问 history 的数据，因此我们把 history 这个 state 放在顶层 Game 组件中。我们可以直接使用history中的数据进行展示，所以我们需要再次进行状态提升，将各种状态数据维护在Game组件类中，进行与第一次状态提升相同的操作，Board中使用传入的值和方法，代码如下

```js
class Board extends React.Component {
    renderSquare(i) {
        return (<Square value={this.props.squares[i]} onClick={() => this.props.onClick(i)} />);
    }

    render() {
        return (<div >
            <div className="board-row" > {
                this.renderSquare(0)
            } {
                    this.renderSquare(1)
                } {
                    this.renderSquare(2)
                }
            </div>
            <div className="board-row" > {
                this.renderSquare(3)
            } {
                    this.renderSquare(4)
                } {
                    this.renderSquare(5)
                }
            </div>
            <div className="board-row" > {
                this.renderSquare(6)
            } {
                    this.renderSquare(7)
                } {
                    this.renderSquare(8)
                }
            </div>
        </div>);
    }
}

class Game extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            history: [{
                squares: Array(9).fill(null),
            }],
            nextIsX: true,
        };
    }
    handleClick(i) {
        const history = this.state.history;
        const current = history[history.length - 1];
        const squares = current.squares.slice();
        if (calculateWinner(squares) || squares[i]) {
            return;
        }
        squares[i] = this.state.nextIsX ? 'X' : 'O';
        this.setState({ history: history.concat([{ squares: squares }]), nextIsX: !this.state.nextIsX })
    }
    render() {
        const history = this.state.history;
        const current = history[history.length - 1];
        const winner = calculateWinner(current.squares);
        let status;
        if (winner) {
            status = 'Winner: ' + winner;
        } else {
            status = 'Next player: ' + (this.state.nextIsX ? 'X' : 'O');
        }
        return (<div className="game" >
            <div className="game-board" >
                <Board squares={current.squares} onClick={(i) => { this.handleClick(i) }} />
            </div>
            <div className="game-info" >
                <div > {status}
                </div>
                <ol > { /* TODO */} </ol>
            </div >
        </div>
        );
    }
}
```

> 然后我们需要将history展示出来，将Game中的Render修改如下：

```js
    render() {
        const history = this.state.history;
        const current = history[this.state.stepNumber];
        const winner = calculateWinner(current.squares);
        const moves = history.map((step, move) => {
            const desc = move ?
                'Go to move #' + move :
                'Go to game start';
            return (
                <li key={move}>
                    <button onClick={() => this.jumpTo(move)}>{desc}</button>
                </li>
            );
        });

        let status;
        if (winner) {
            status = 'Winner: ' + winner;
        } else {
            status = 'Next player: ' + (this.state.nextIsX ? 'X' : 'O');
        }
        return (<div className="game" >
            <div className="game-board" >
                <Board squares={current.squares} onClick={(i) => { this.handleClick(i) }} />
            </div>
            <div className="game-info" >
                <div > {status}
                </div>
                <ol > {moves} </ol>
            </div >
        </div>
        );
    }
```

> 展示完成之后，点击某一步需要跳回那一步，所以就要调整jumpTo方法,并维护stepNumber，点击时跳转

```js
    jumpTo(step) {
        this.setState({
            stepNumber: step,
            xIsNext: (step % 2) === 0,
        });
    }
```

> 致此，一个井字棋跳转历史步骤的功能就做完了，演示代码可观看官方代码<https://codepen.io/gaearon/pen/gWWZgR?editors=0010>