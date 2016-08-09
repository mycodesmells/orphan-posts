# CSS Magic With Flexbox

I've never been a fan of creating styles for my applications. Over time I've came up with dozens of excuses why my apps are so ugly, or at least just lack of something special. Ever since I saw Bootstrap, I tend to use is almost every time, because it makes the look and feel much better in seconds. Why do I struggle with CSS? It might be that I don't have a feel for the UX, or that every time I try to style out a layout, something goes wrong and I loose my patience. Apparently at least one of my problems can be cured with a CSS3 feature - Flexbox layout.

### What is flexbox?

Flexible Box Layout is a CSS _module_ that groups a quite long list of CSS properties that alter the way it's displayed. The main story behind this layout is to create a solution for showing elements within a dynamic container, that is one which size is not known or not constant. 

### Basic properties

I don't believe that all attributes need to be fully described here - I prefer to give you a small dose as a preview of what can be done. If you are interested in the concept, I dare you to play around with it so it could fit your needs perfectly.

The most important thing is enabling the layout on a container:

    .container {
        display: flex;
    }

But there are three basic attributes for the container that are a must if you want to have a better control over what is going on:

- `flex-direction` determines if you want your flex items (things within a container) to be placed horizontally (`row)`) or vertically (`column`).
- `justify-content` is _the thhing_ you need to know. It determines if the items are aligned to the start or the end, or maybe they need to be distributed evenly? Should the edge elements have margins between them and container's borders? Just play with it.
- `align-items` can change the way the item are stretched (or not) along the secondary axis.

When it comes to the items, they are hardly as important as those for container, but they surely bring something to the table:

- `order` allows you to manually decide how the items are sorted within their container,
- `flex-grow` allows you to put _weights_ to an item's dimension along the main axis - with your items filling the whole container, you can decide that one item should be proportionally bigger than the others,
- `aign-self` allows you to override container's `align-items` for that particular item.
      

### Use cases

In my personal experience I think of flexbox as of solution to two pesky issues with page layout: distributing elements evenly either horizontally, and with centering an element vertically within some parent.

If you don't want to use flexbox, that first issue requires some ugly hacks with `text-align` and `margin`. And this works poorly once we add new items - at some point the margin forces the container to wrap, as the items cannot fit in one line any more. With flexbox it's much easier:

*index.html*:

    <nav>
        <div class="menuitem first">first</div>
        <div class="menuitem second">second</div>
        <div class="menuItem third">Third</div>
    </nav>


*style.css*:

    nav {
        display: flex;
        justify-content: space-around;
        align-items: center;
        ...
    }

*Result:*

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/flexbox-use-case-1.png"/>

Centering vertically would also require some strange solutions (properties `top`, and `transform`) if you avoid flexbox. Doing it the new way is much simpler;

*index.html*:

    <nav>
        <div class="menuItem centered">Centered</div>
    </nav>


*style.css*:

    nav {
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      align-items: center;
      ...
    }
    
*Result:*

<img src="https://raw.githubusercontent.com/mycodesmells/orphan-posts/master/img/flexbox-use-case-2.png"/>

### Summary

The only downside of flexbox at the moment is [the browser support](http://caniuse.com/#feat=flexbox), which could be a blocker if you need to support older IEs or Andoid native browsers. 
