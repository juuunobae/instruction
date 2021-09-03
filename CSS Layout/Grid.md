# grid
- 2차원(행과 열)의 레이아웃 시스템을 제공한다. 

```html 

    <div class='container'>
        <div class='item'></div>
        <div class='item'></div>
        <div class='item'></div>
        <div class='item'></div>
    </div>

```

## minmax
- 최솟값과 최댓값을 지정할 수 있는 함수
```css

    .container {
        grid-template-columns: repeat(3, minmax(100px, 1fr));
    }

```

## auto-fill
## auto-fit
- column의 개수를 미리 정하지 않고 설정된 너비가 허용하는 한 최대한 크기의 layout을 만든다.
```css

    .container {
        /* 공간을 남겨둔다. */
        grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));

        /* 남는 공간을 채운다.*/
        grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
    }

```

## min-content
## max-content
- 콘텐츠의 크기에 대한 최솟값과 쵀댓값 설정
```css

    .container {
        grid-template-columns: min-content max-content 200px;
    }

```

# grid container
- grid를 적용하는, grid의 전체 영역이다.
```css

    .container {
        display: grid;
    }

```

## grid-template-columns
- 그리드의 열의 형태를 정의 해주는 것이다.
```css

    .container {
        grid-template-columns: 200px 200px 200px;
        grid-template-columns: 1fr 1fr 1fr;
        grid-template-columns: repeat(3, 1fr);
    }

```

## grid-template-rows
- 그리드의 행의 형태를 정의 해주는 것이다.
```css

    .container {
        grid-template-rows: 200px 200px 200px;
        grid-template-rows: 1fr 1fr 1fr;
        grid-template-rows: repeat(3, 1fr);
    }

```

## grid-auto-columns
## grid-auto-rows
- template으로 지정한 요소들의 갯수보다 요소들이 더 많을 경우 그 요소들의 크기를 지정한다.
- 요소들의 갯수를 알 수 없을 때 사용한다.
## grid-auto-flow
- 아이템이 자동 배치되는 방향을 정한다.
```html

    <div class='container'>
        <div class="item">1</div>
        <div class="item">2</div>
        <div class="item">3</div>
        <div class="item">4</div>
        <div class="item">5</div>
        <div class="item">6</div>
        <div class="item">7</div>
        <div class="item">8</div>
        <div class="item">9</div>
        <div class="item">10</div>
        <div class="item">11</div>
        <div class="item">12</div>
        <div class="item">13</div>
        <div class="item">14</div>
        <div class="item">15</div>
    </div>

```
```css

    .container {
        grid-template-columns: repeat(3, 100px);
        grid-template-rows: repeat(3, 100px);

        /* row */
        grid-auto-flow: row;
        grid-auto-rows: 100px;

        /* column */
        grid-auto-flow: column;
        grid-auto-columns: 100px;

    }

```

## gap
- grid 사이의 간격을 정해준다.
```css

    .container {
        column-gap: 10px;
        row-gap: 10px;
        /* column, row 한번에 설정 */
        gap: 10px
    }

```

## grid-template-areas
- 각 영역에 이름을 붙이고, 그 이름을 이용해서 배치하는 방법이다.
```css

    .container {
        grid-template-columns: 200px 200px 200px;
        grid-template-rows: 200px 200px 200px;
        grid-template-areas: 
            "header header header"
            "content content nav"
            "footer footer footer";
    }

    .header {
        grid-area: header;
    }

    .content {
        grid-area: content;
    }

    .nav {
        grid-area: nav;
    }

    .footer {
        grid-area: footer;
    }

```

## grid-tmplate
- 각 영역에 이름을 붙이고, row와 column의 크기를 정해주고 그 이름을 이용해서 배치하는 방법이다.
```css

    .container {
        grid-template-columns: 200px 200px 200px;
        grid-template-rows: 200px 200px 200px;
        grid-template: 
            "header header header" 1fr
            "content content nav" 2fr
            "footer footer footer" 1fr;
        
        grid-template: 
            "header header header" 1fr
            "content content nav" 2fr
            "footer footer footer" 1fr / 1fr 1fr 1fr 1fr; /*  ... / column의 크기 */
    }

    .header {
        grid-area: header;
    }

    .content {
        grid-area: content;
    }

    .nav {
        grid-area: nav;
    }

    .footer {
        grid-area: footer;
    }

```

## justify-content 
- grid 아이템들의 너비를 모두 합한 값이 grid 컨테이너의 너비보다 작을 때 grid 아이템들을 통째로 정렬한다.
## align-content
- grid 아이템들의 높이를 모두 합한 값이 grid 컨테이너의 높이보다 작을 때 grid 아이템들을 통째로 정렬한다.
## place-content
- 위의 두 속성을 같이 쓸 수 있다.
```css

    .container {
        display: grid;
        justify-content: stretch;
        align-content: stretch;
        place-content: [align-contnet] [justify-content]
    }

```

# grid item
- grid 컨테이너의 자식 요소들
- 그리드 규칙에 의해 배치되는 태그들

## grid-column-start, grid-column-end
## grid-row-start, grid-row-end
## grid-column, grid-row
- 각 셀의 영역을 지정한다.
  - 값으로 몇번 째 줄인지 입력한다.
```css

    .container {
        grid-template-columns: 200px 200px 200px;
        grid-template-rows: 200px 200px 200px;
    }

    .header {
        grid-column-start: 1;
        grid-column-end: 4;
        grid-row-start: 1;
        grid-row-end: 2;

        grid-column: 1 / -1;
        grid-row: 1 / 2;

        grid-column: span 3;
        grid-row: 1 / 2;
    }

    .content {
        grid-column-start: 1;
        grid-column-end: 3;
        grid-row-start: 2;
        grid-row-end: 4;

        grid-column: 1 /  3;
        grid-row: 2 / -1;

        grid-column: 1 /  3;
        grid-row: 2 / span 2;
    }

```

> grid-column, grid-row: start / end;</br>
> span = 몇 개의 셀을 차지하게 할 것인지 지정

## line naming
- 각 라인에 이름을 부여해주고 사용할 수 있다. 
```css

    .container {
        grid-template-columns: [first-line] 200px [second=line] 200px [third-line]  200px [fourth-line];
        grid-template-rows: 200px 200px 200px;
    }

```

## align-self
- 해당 아이템을 세로(column 축) 방향으로 정렬한다.
## justify-self
- 해당 아이템을 가로(row 축) 방향으로 정렬한다.
## place-self
- 위의 두 속성을 같이 쓸 수 있다.
```css

    .container {
        grid-template-columns: repeat(4, 100px);
        grid-template-rows: repeat(4, 100px);
    }

    .header {
        align-self: stretch;
        justify-self: stretch;
        place-self: [aligin-self] [justify-self]
    }


```
 