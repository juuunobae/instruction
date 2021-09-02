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
        grid-template-columns: repeat(3, 1fr) repeat(3, 1fr) repeat(3, 1fr);
    }

```

## grid-template-rows
- 그리드의 행의 형태를 정의 해주는 것이다.
```css

    .container {
        grid-template-rows: 200px 200px 200px;
        grid-template-rows: 1fr 1fr 1fr;
        grid-template-rows: repeat(3, 1fr) repeat(3, 1fr) repeat(3, 1fr);
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
