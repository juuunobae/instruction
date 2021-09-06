# SCSS
- CSS preprocessor
- compile 하거나 build하는 작업이 필요하다.
  - gulp를 이용해 compile 해준다.

## gulp setting
### 설치
- `npm init`
- `npm install gulp`
- `npm install gulp-autoprefixer gulp-csso gulp-sass`
- `npm install node-sass`
- `npm install @babel/core @babel/preset-env @babel/register`
- `npm install del`

### 파일
- `.babelrc`
    ```js

        {
            "presets": ["@babel/preset-env"]
        }

    ```
- `.gitignore`
    ```js

        node_modules

    ```
- `gulpfile.babel.js`
    ```js

        import gulp from "gulp";
        import del from "del";
        import sass from "gulp-sass";
        import minify from "gulp-csso";
        import autoprefixer from "gulp-autoprefixer";

        sass.compiler = require("node-sass");

        const routes = {
        css: {
            watch: "src/scss/*",
            src: "src/scss/styles.scss",
            dest: "dest/css"
        }
        };

        const styles = () =>
        gulp
            .src(routes.css.src)
            .pipe(sass().on("error", sass.logError))
            .pipe(
            autoprefixer({
                flexbox: true,
                grid: "autoplace"
            })
            )
            .pipe(minify())
            .pipe(gulp.dest(routes.css.dest));

        const watch = () => {
        gulp.watch(routes.css.watch, styles);
        };

        const clean = () => del(["dest/"]);

        const prepare = gulp.series([clean]);

        const assets = gulp.series([styles]);

        const live = gulp.parallel([watch]);

        export const dev = gulp.series([prepare, assets, live]);

    ```
- `index.html`
    ```html

        <head>
            <link rel="stylesheet" href="dest/css/styles.css" />
        </head>

    ```
- `src/scss.styles.scss` 
- `npm run dev` or `yarn dev` 실행
  - `package.json`
    ```js

        "scripts": {
            "dev": "gulp dev"
        },

    ```
    
## SCSS syntax
