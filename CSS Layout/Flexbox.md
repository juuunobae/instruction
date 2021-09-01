# flexbox
- 뷰포트나 요소의 크기가 불명확하거니 동적으로 변할 때에도 효율적으로 요소를 배치, 정렬, 분산할 수 있는 방법을 제공하는 css 레이아웃이다.

```html 

    <div class='container'>
        <div class='item'></div>
        <div class='item'></dib>
        <div class='item'></dib>
    </div>

```

# flex container
- flex를 정의하는 태그
- 정렬이나 배치를 하고 싶은 요소의 부모 태그
```css

    .container {
        display: flex;
    }

```
> item 태그들의 위치를 움직이려면 container 태그에 `display: flex`를 해주어야 한다.

## flex-direction
 - flex item들을 배치할 때 사용할 주축 및 방향을 지정한다.
 - 기본값을 `row`이다.
 - 값
   - row
   - row-reverse
   - column
   - column-reverse

### row
- 주축이 수평이 된다.
- main axis(메인축) = 가로(수평축)
  - justify-content 
    - 가로축을 기준으로 정렬한다.
- cross axis(교차축) = 세로(수직축)
  - align-items
    - 세로축을 기준으로 정렬한다.
```css

    .container {
        desplay: flex;
        flex-direction: column;
        /* main axis(메인축) */
        justify-content: ;
        /* cross axis(교차축) */
        align-items: ;
    }

```

### column
- 주축이 수직이 된다.
- main axis(메인축) = 세로(수직축)
  - justify-content 
    - 세로축을 기준으로 정렬한다.
- cross axis(교차축) = 가로(수평축)
  - align-items
    - 가로축을 기준으로 정렬한다.
```css

    .container {
        desplay: flex;
        flex-direction: column;
        /* main axis(메인축) */
        justify-content: ;
        /* cross axit */
        align-items: ;
    }

```

## flex-wrap
- 기본값은 nowrap이다.
- 값
  - nowrap
    - item들의 넓이가 container의 넓이보다 클 경우 item들의 넓이를 container의 넓이에 맞게 축소 된다.
  - wrap
    - item들의 총 넓이가 container의 넓이보다 클 경우 item들을 다음 줄에 이어서 정렬해준다.
  - nowrap-reverse
  - wrap-reverse
```css

    .item {
        flex-wrap: wrap;
    }

```

### align-content
- 여러줄이 된 item들을 cross axis(교차축)을 기준으로 정렬
```css

    .container {
        flex-wrap: wrap;
        align-content: ;
    }

```

# flex item
- flex container안에 있는 각각의 태그
- 정렬이나 배치를 할 태그

## align-self
- item 태그 하나를 cross axis(교차축)을 기준으로 정렬
```css

    .item:nth-child(2) {
        align-self: center;
    }

```

## order
- item 태그의 순서를 지정
```css

    .item:nth-child(2) {
        order: 1;
    }

```

## flex-shrink
- 기본값은 1이다.
- container가 nowrap일 경우 item들의 넓이가 줄어들 때 해당 item의 줄어듬의 수치를 정해준다.
```css

    .item:nth-child(1) {
        flex-shrink: 2
    }

```

## flex-grow
- 기본값은 0이다.
- flex-shrink와 반대로, container에 공백이 있을 경우 해당 item이 넓어짐으로 공백을 메운다.
```css

    .item:nth-child(2) {
        flex-grow: 1;
    }

```

## flex-basis
- 기본값은 auto이다. (해당 item의 width 값)
  - main axis가 가로축일 때는 width, 세로축일 때는 height
- item의 기본크기를 설정한다.
```css

    .item {
        flex-basis: auto;
    }