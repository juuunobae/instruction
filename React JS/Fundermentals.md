# Requirement
- 리액트를 사용하기 위해 필요한 컨셉들이 있다.

## Object Destructuring
### Structure
- 적은 코드를 사용해 더 깔끔하게 보이도록 한다.
```js

    const human = {
        name: ,
        lastName: ,
        nationality: 'Korean'
        favFood: {
            breakfast: ,
            lunch: ,
            dinner: ,
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

    console.log(name, lastName);

```