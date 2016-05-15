# CSS Magic With Grid Layout

As a Front End Developer (but not only is this field of course), you need to learn new new things all the time. Even if you want to stick with Angular, you need to keep your knowledge up to date with the latest updates. Same case for HTML, which introduced many changes in its fifth version. How about CSS? Is is evolving as well? Of course it is, and one of the new features you should be familiar with is Grid Layout.

### What is grid?

Think of Grid Layout as of a much, much better alternative than putting your page elements within `<table>` element. It is a bit more complex, but once you get a general idea, you'll find it pretty convenient. It requires you to learn some new CSS attributes, but their names generallt speak for themselves.

I one of the previous posts, we have already taken a look on [Flexbox](http://mycodesmells.com/post/css-magic-with-flexbox/), so how's this different? As you may recall, Flexbox is a perfect solution for one-dimentional layout: a single column of a single row (although it may be wrapped, you are always working with a group of element in a single, linear order). With Grid Layout, you get a possibility to place your element in two dimentions, so that you can design your whole page using it. Grid and Flexbox work perfectly together, if you ever want to mix and match them within your page.

There is one major disadvantage of Grid, which causes its limited popularity for now, and it's its suport across browsers. As for May 2016, it is supported only in IE 10+ and Microsoft Edge, but those browsers use an older syntax which is likeley to be changed. And this is the main reason for the lack of support in the other browsers - the specification of Grid Layout is still a work in progress, so that they choose not to follow it oficially. The support, as an experimental feature, can be switched though:

- enable _experimental web platform features_ under `chrome://flags` address in Google Chrome,
- same as above under `opera://flags` in Opera,
- enable `layout.css.grid.enabled` in Firefox

Why should you bother to learn something that is still not widely supported? Because Grid is such a significant improvement over current layout solutions, that it's just a matter of time when we finally get its feature full support everywhere. And when we do, you really need to start using it as soon as possible. I suggest you to be ahead of time in this case.

### Basic terminology

In order to understand Grid Layout better, you need to know what is what in the module:

- **container** is a parent for all elements, the root element for grid,
- **grid item** is a direct child of a container
- **grid line** is a separator between rows or columns
- **grid track** is a single column or a row,
- **grid area** is composed of one or more cells, an area is limited by two horiontal lines and two vertical grid lines (therefore grid area is always a rectangle)

### Simple example

Imagine we want to to have the following layout on our page:

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/grid-layout-example.png"/>

As you can see, we want to have a header, a footer, two sidebar panels and a place in the middle for some content. How should we approach this? We have two approaches we could go with. In both cases the HTML part looks the same:

    <div class="container">
        <div class="header"></div>
        <div class="left"></div>
        <div class="content"></div>
        <div class="right"></div>
        <div class="footer"></div>
    </div>

First, we can name our areas:

    .header {
        background-color: red;
        grid-area: header;
    }

    .footer {
        background-color: red;
        grid-area: footer;
    }

    .left {
        background-color: orange;
        grid-area: left;
    }

    .content {
        grid-area: content;
    }

    .right {
	    background-color: orange;
	    grid-area: right;
    }

With those names in store, we can define their layout in a class definition for `container` element:

    .container {
        display: grid;
        grid-template-areas:
         "header header header"
         "left content right"
         "footer footer footer";
        grid-template-columns: 120px auto 120px;
        grid-template-rows: 120px auto 120px;
        justify-content: stretch;
        align-content: stretch;
    }

As you can see, in `grid-template-areas` areas' positions are defined. In order to have `header` and `footer` elements have the same width as three elements in the second row (`left`, `content` and `right`), their names need to be repeated three times. Then you define the sizes for rows and colmns, and finally two attributes that allow our grid to take up all available space on the page.

 Although we did manage to get the final result as we wanted, that's not the most elegant and generic way to place your areas in the grid. There is a much more dynamic way to do so. In this casse, instead of naming the areas, we define the lines between which that area is placed (using `grid-column` and `grid-row` attributes):

    .header {
        background-color: red;
        grid-column: 1 / 4;
        grid-row: 1 / 2;
    }

    .footer {
        background-color: red;
        grid-column: 1 / 4;
        grid-row: 3 / 4;
    }

    .left {
        background-color: orange;
        grid-column: 1 / 2;
        grid-row: 2 / 3;
    }

    .content {
        grid-column: 2 / 3;
        grid-row: 2 / 3;
    }

    .right {
        background-color: orange;
        grid-column: 3 / 4;
        grid-row: 2 / 3;
    }

For example, `right` element is placed between third and fourth vertical lines (that's why it's `3 / 4` for `grid-column`) and between second and third horizontal lines (`2 / 3` for `grid-row`).

With these specific descriptions in each area class, the definition for `container` looks much clearer:

    .container {
        display: grid;
        grid-template-columns: 120px auto 120px;
        grid-template-rows: 120px auto 120px;
        justify-content: stretch;
        align-content: stretch;
    }

### Summary

As you can see, the Grid Layout itself is not as difficult as one may think. I strongly recommend you to take a look at [the official docs](https://www.w3.org/TR/css-grid-1/) and all attributes that are introduced with this new CSS module. The more you learn, the more you can play aroud with it, and the better your page will look.
