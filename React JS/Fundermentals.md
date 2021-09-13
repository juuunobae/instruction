# Requirement
- 리액트를 사용하기 위해 필요한 컨셉들이 있다.

## Object Destructuring
### Structure
- 적은 코드를 사용해 더 깔끔하게 보이도록 한다.
```js

    const human = {
        name: 'a',
        lastName: 'b',
        nationality: 'Korean'
        favFood: {
            breakfast: 'c',
            lunch: 'd',
            dinner: 'e',
        }
    }

    // const name = human.name;
    // const lastName = human.lastName;
    // const difName = human.nationality;
    // const breakfast = humen.favFood.breakfast;

    // 크드를 줄여서 사용할 수 있다.
    // 변수명과 키 명을 다르게 사용할 수도 있다.
    // 중첩된 객체에도 사용가능하다.
    const { 
        name, 
        lastName, 
        nationality: difName, 
        favFood: {  breakfast, lunch, dinner }, 
    } = human;

```

## Spread Operator
- 배열이나 객체를 펼쳐준다.
```js

    const days = ['mon', 'tue', 'wed'];
    const otherDays = ['thu', 'fri', 'sat'];

    const allDays = [...day, ...otherDay]; // ['mon', 'tue', 'wed', 'thu', 'fri', 'sat']

    const a = {
        first: 'one',
    }

    const b = {
        second: 'two',
    }

    const c = {...a,  ...b}; // {first: 'one', second: 'two'}

```

## Classes
- 객체지향프로그래밍
- construtor
  - class를 생성할 때 필요한 무언가를 가지고 있다.
- 상속가능하다.
```js

    class Human {
        constructor(name, lastName) {
            this.name = name;
            this.lastName = lastName
        }
    }

    const bcd = new Human('bcd', 'ab'); // { name: 'bcd', lastName: 'ab', ...}

    class baby extends Human {
        cry() {
            console.log('aaaaa');
        }
    } 

    const myBaby = new Baby('ba', 'by');

    console.log(myBaby); // { name: 'ba', lastName: 'by', ...}
    console.log(myBaby.cry); // aaaaa

```
> 상속받은 class는 부모 class의 속성들을 확장해서 사용할 수 있다.

## Array
### .map
- 배열의 요소에 한번 씩 접근해서 함수를 실행한 뒤 결과값을 새로운 배열로 만들어준다.
- 함수의 첫번 째 파라미터는 배열의 각 요소들이 순서대로 들어가고, 두번 째 파라미터는 index 숫자가 순서대로 들어간다.
```js

    const days = ['mon', 'tue', 'wed', 'thu', 'fri', 'sat'];

    const emojiDays  = days.map(day => `😄${day}`);

```

### .filter
- 배열의 모든 요소에 함수가 실행되면서 true를 리턴하는 요소들로 새로운 배열을 만든다.
```js

    const numbers = [2, 45, 4535, 6436, 435, 53, 453, 45, 4, 5, 43, 2, 3, 4, 5, 6];

    const biggerThan20 = numbers.filter(number => number > 20);

```

### .forEach
- 배열의 각 요소에 접근해 함수를 실행만 시킨다.
```js

    const days = ['mon', 'tue', 'wed', 'thu', 'fri', 'sat'];

    const emojiDays  = days.map(day => console.log(`😄${day}`));

```
> map과 filter는 새로운 배열을 반환하지만 forEach는 실행만 시킨다.

### .includes
- 어떠한 요소가 배열안에 있는지 확인
### .push
- 어떠한 값을 배열의 맨뒤에 추가한다.
```js

    greetings = ['Hi', 'Hello', 'Bye', 'Good'];


    if(!greetings.includeds(Hello)){
        greetings.push('Hello');
    }

```