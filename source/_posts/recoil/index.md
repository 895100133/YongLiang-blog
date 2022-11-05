---
title: React状态管理之Recoil
date: 2020-12-25 11:26:37
tags:
categories:
- [React, 状态管理工具, Recoil]
---

# Recoil学习笔记

## 安装
### npm
  ```node
    npm install recoil
  ```
  or
  ```
    yarn add recoil
  ```

## 使用
  ### 定义数据 atom:
  atom 可以在任何地方定义状态数据
  ```javascript
  const todoListState = atom({
    key: 'todoListState',
    default: [],
  })
  ```
  在需要的地方，可以使用useRecoilValue() hook读取我们定义的数据
  ```javascript
    function TodoList() {
      const todoList = useRecoilValue(todoListState);
      return (
        <>
          { / * <TodoListStats /> * / }
          { / * <TodoListFilters /> * / }
          < TodoItemCreator />
          {todoList.map((todoItem) => (
            <TodoItem key={todoItem.id} item={todoItem} />
          ))}
        </>
      )
    }
  ```
  可以使用useSetRecoilValue() hook设置我们定义的数据
  ```javascript
    function TodoItemCreator() {
      const [inputValue, setInputValue] = useState('')
      const setTodoList = useSetRecoilValue(todoListState);
      const addItem = () => {
        setTodoList((oldTodoList) => [
          ...oldTodoList,
          {
            id: getId(),
            text: inputValue(),
            isComplete: false,
          }
        ]);
        setInputValue('');
      };
      const onChange = ({target: {value}}) => {
        setInputValue(value);
      };
      return (
        <div>
          <input type="text" value={inputValue} onChange={onChange} />
          <button onClick={addItem}>Add</button>
        </div>
      );
    }
    // 获取唯一id
    let id = 0;
    function getId() {
      return id++;
    }
  ```
  useRecoilValue() hook获取读取数据和设置数据，用法像useState()
  ```javascript
    function TodoItem({item}) => {
      const [todoList, setTodoList] = useRecoilState(todoListState);
      const index = todoList.findIndex((listItem) => listItem === item);
      const editItemText = ({target: {value}}) => {
        const newList = replaceItemIndex(todoList, index, {
          ...item,
          text: value,
        })
        setTodoList(newList);
      };
      const toggleItemCompletion = () => {
        const newList = replaceItemIndex(todoList, index, {
          ...item,
          isComplete: !item.isComplete,
        });
        setTodoList(newList);
      };
      const deleteItem = () => {
        const newList = removeItemAtIndex(todoList, index);
        setTodoList(newList);
      };
      retruen (
        <div>
          <input type="text" value={item.text} onChange={editItemText} />
          <input
            type="checkbox"
            checked={item.isComplete}
            onChange={toggleItemCompletion}
          />
          <button onClick={deleteItem}>x</button>
        </div>
      );
    }
    function replaceItemAtIndex(arr, index, newValue) {
      return [...arr.slice(0, index), newValue, ...arr.slice(index + 1)];
    }
    function removeItemAtIndex(arr, index) {
      return [...arr.slice(0, index), ...arr.slice(index + 1)];
    }
  ```

  Selectors就像redux的action，获取依赖的值，返回经过处理后的值
  用法如下

  ```javascript
    const filterTodoListState = select({
      key: 'filterState',
      get: ({get}) => {
        const filter = get(todoListState);
        const list = get(todoList)
        switch (filter) {
          case 'Show Completed':
            return list.filter((item) => item.isComplete);
          default:
            return list;
        }
      }
    })
  ```