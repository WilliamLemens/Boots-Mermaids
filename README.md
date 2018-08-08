# Bootstraps & Mermaids
A demonstration of how to get Mermaid charts working properly when embedded in Bootstrap tab elements.
##### [Live link](https://williamlemens.com/assets/mermaid-sample.html)
## A brief explanation:
Mermaid charts will render even if they're not visible, so any charts embedded in invisible elements will simply render with dimensions of `-Infinity`. This is not very conducive to pretty charts when the element becomes visible, as they aren't reloaded.

As such, this is a demo of my workaround. The important code is all in index.html.
### How it works
By initially setting Mermaid charts `div`'s classes to `.pre-mermaid` instead of `.mermaid`, we avoid premature rendering.

The script at the bottom of index.html is where the work happens.
```
<script>
  function transferToMermaid(el) { // switches element from .pre-mermaid to .mermaid
    el.removeClass('pre-mermaid');
    el.addClass('mermaid');
    return el;
  }
  $('.pre-mermaid:visible').each( function( index, el) { // Renders all initially visible graphs
    transferToMermaid($(el));
  });
  mermaid.init();
  $('a[data-toggle="tab"]').on('shown.bs.tab', function (e) { // renders graph as you click the tab it lives in
    // initializes each mermaid chart on this tab after changing the class from .pre-mermaid to .mermaid
    mermaid.init(undefined, transferToMermaid($($(e.target).attr("href")+" > .pre-mermaid")));
  });
</script>
```
I'll go through this line-by-line for prosperity.
```
  function transferToMermaid(el) {
    el.removeClass('pre-mermaid');
    el.addClass('mermaid');
    return el;
  }
```
This function takes in an element and changes its class from `.pre-mermaid` to `.mermaid`.
```
  $('.pre-mermaid:visible').each( function( index, el) {
    transferToMermaid($(el));
  });
```
This iterates over every visible `.pre-mermaid` element on our page. It then calls `transferToMermaid()` on them, readying them for initialization.
```
  mermaid.init();
```
Renders all `.mermaid` elements on the page. At this point, the only extant `.mermaid` elements will be those that are currently visible.
```
  $('a[data-toggle="tab"]').on('shown.bs.tab', function (e) {
    mermaid.init(undefined, transferToMermaid($($(e.target).attr("href")+" > .pre-mermaid")));
  });
```
This function is triggered every time `shown.bs.tab`, an event Bootstrap calls after a tab has been switched to, occurs. It is fed `e`, the tab element.

The function then uses `$($(e.target).attr("href")+" > .pre-mermaid")` to get the `.pre-mermaid` elements in `e`. It feeds the `.pre-mermaid` elements to `transferToMermaid()`, prepping them for initialization, and then calls `mermaid.init()` on the now `.mermaid` elements.
