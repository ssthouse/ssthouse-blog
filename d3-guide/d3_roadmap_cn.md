
# The Hitchhiker’s Guide to d3.js

the d3 learning landscape in all its glory

The landscape for learning d3 is rich, vast and sometimes perilous. You may be intimidated by the long list of functions in [d3’s API documentation](https://github.com/d3/d3/blob/master/API.md) or paralyzed by choice reviewing the [dozens of tutorials](https://github.com/d3/d3/wiki/Tutorials) on the home page. There are over [20,000+ d3 examples](http://blockbuilder.org/search) you could learn from, but you never know how approachable any given one will be.

![we are still working on mapping out the whole landscape…](https://cdn-images-1.medium.com/max/4814/1*C17GW5l4S_W99_52lQMpBQ.png)*we are still working on mapping out the whole landscape…*

If all you need is a quick bar or line chart, maybe this article isn’t for you, there are [plenty of charting libraries](https://github.com/wbkd/awesome-d3#charts) out there for that. If you’re into books, check out [Interactive Data Visualization for the Web](http://shop.oreilly.com/product/0636920026938.do) by [Scott Murray](https://twitter.com/alignedleft) as a great place to start. [D3.js in Action](https://www.manning.com/books/d3-js-in-action) by [Elijah Meeks](https://twitter.com/Elijah_Meeks) is a comprehensive way to go much deeper into some regions of the API.

This guide is meant to prepare you mentally as well as give you some fruitful directions to pursue. There is a lot to learn besides the d3.js API, both technical knowledge around web standards like HTML, SVG, CSS and JavaScript as well as communication concepts and data visualization principles. Chances are you know something about some of those things, so this guide will attempt to give you good starting points for the things you want to learn more about.

## Communicate Complex Concepts

![[r2d3.us visual introduction to machine learning](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/) sets the bar high](https://cdn-images-1.medium.com/max/3676/1*ofBagXi1x0h8TdHF8tc3nA.png)*[r2d3.us visual introduction to machine learning](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/) sets the bar high*

Before we dive into data visualization principles and technical skills, lets take a second to be aspirational. There are some amazing examples of what’s possible in this medium, whether its a [New York Times article](https://www.google.com/search?q=new+york+times+d3+interactives&oq=new+york+times+d3+interactives&aqs=chrome..69i57j69i64.6825j0j1&sourceid=chrome&ie=UTF-8), [r2d3](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/), [distill.pub](http://distill.pub), [datasketch|es](http://www.datasketch.es/), [polygraph](https://pudding.cool/), or [ncase](http://ncase.me/). Be sure to leave a comment with any inspirations I didn’t list here!

Don’t just look to others though, one of the most important things you can do is set an aspiration for yourself. I found out from [interviewing some of the top data visualization practitioners using d3.js](https://medium.com/@enjalot/how-do-you-learn-d3-js-ccffc151419b) that one of the best ways to learn is to set your sights something you really want to build and figure out what you need to build it.

## Visual Representations
> D3 does not introduce a new visual representation. Unlike [Processing](http://processing.org/), [Raphaël](http://raphaeljs.com/), or [Protovis](http://vis.stanford.edu/protovis/), D3’s vocabulary of graphical marks comes directly from web standards: HTML, SVG, and CSS
 — http://d3js.org

![Learning d3.js to write charts is like to learning French to write nutrition labels. To be fair, [@syntagmatic](https://twitter.com/syntagmatic) has made some beautiful [nutrition visualizations](http://bl.ocks.org/syntagmatic/2420080)](https://cdn-images-1.medium.com/max/2000/1*GaASA7rqnQVDpHQs9BdbqA.png)*Learning d3.js to write charts is like to learning French to write nutrition labels. To be fair, [@syntagmatic](https://twitter.com/syntagmatic) has made some beautiful [nutrition visualizations](http://bl.ocks.org/syntagmatic/2420080)*

Charts are just rectangles with shapes inside of them. There are a handful of configurations of those shapes we recognize as common charts or graphs. What d3 provides is a way to define your own visual representations by manipulating graphical marks or creating your own shapes. It makes it easy to add interactions to visuals and declare how your visuals behave. You are here to learn how to express things that aren’t possible to express in any other medium.

If you want to learn about some of the principles behind the different kinds of marks and graphical representations people use, you can’t go wrong exploring the [Grammar of Graphics](https://smile.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448?sa-no-redirect=1).

Don’t worry though, there are a ton of creative things you can do with just circles, rectangles and some careful positioning. Start simple, always try to get something to show up on the screen and build up from there.

## On the web

![[SVG Beyond Mere Shapes](https://www.visualcinnamon.com/2016/04/svg-beyond-mere-shapes.html) is an awesome demonstration of web standards graphics manipulation](https://cdn-images-1.medium.com/max/4432/1*TwUCVhrN9Xltsj3sDgntUQ.png)*[SVG Beyond Mere Shapes](https://www.visualcinnamon.com/2016/04/svg-beyond-mere-shapes.html) is an awesome demonstration of web standards graphics manipulation*

One of the reasons you want to use d3.js is so you can share your work instantly with anyone who uses a web browser (at least half of the people on Earth!). That means you need to have at least a passing understanding of the medium you’ll be working in, which is HTML5. So before you even start calling d3 API functions you’ll need some basics in SVG, HMTL and CSS. You’ll probably also want to [learn some Canvas](https://www.w3schools.com/html/html5_canvas.asp) if you want to be serious about rendering a lot of data (don’t worry, its actually kind of easier to learn than SVG). I recommend [this is a great intermediate tutorial](https://medium.freecodecamp.com/d3-and-canvas-in-3-steps-8505c8b27444) on using d3 with Canvas after you are comfortable with the basics of both.

For SVG I recommend starting with this short but sweet [SVG primer](http://alignedleft.com/tutorials/d3/an-svg-primer) by [Scott Murray](http://twitter.com/alignedleft). Play around with manually creating SVG elements and seeing how they work. Use a tool like [BlockBuilder](http://blockbuilder.org) to quickly get started without setting up any kind of development environment. You may want to refer to the [MDN reference site for SVG](https://developer.mozilla.org/en-US/docs/Web/SVG). Once you’ve mastered the basics, check out [SVG beyond mere shapes](https://www.visualcinnamon.com/2016/04/svg-beyond-mere-shapes.html) by [Nadieh Bremer](http://visualcinnamon.com).

![[http://blockbuilder.org](http://blockbuilder.org) makes it easy to play with web standards!](https://cdn-images-1.medium.com/max/2140/1*PFwvxtqLRRVekNoMufclzw.gif)*[http://blockbuilder.org](http://blockbuilder.org) makes it easy to play with web standards!*

You don’t have to use SVG to make your visualization, it’s relatively common to manipulate HTML elements like *<div> *tags with d3. You will need to be familiar with [CSS positioning](https://css-tricks.com/almanac/properties/p/position/) to make it work well. You can even [mix HTML, SVG and Canvas all at once](http://bl.ocks.org/sxv/4491174)!

It can be a bit overwhelming to figure out which rendering system you should use, and even how to use any one of them. I’m going to reiterate the importance of knowing basic HTML, CSS, SVG (and a little Canvas) before getting started with d3.js!

## Getting started with d3.js

![[d3js.org](http://d3js.org)](https://cdn-images-1.medium.com/max/4424/1*KfsnI5vicI0ozs1uP85Pfg.png)*[d3js.org](http://d3js.org)*

How do you build up your visualization from first principles? Piece by piece and a whole lot of utility functions. As you’ve probably seen, [d3’s API](https://github.com/d3/d3/blob/master/API.md) is massive, so lets call out a few of the utilities that will be particularly helpful early on.

### d3-scale

![[colors](http://blockbuilder.org/enjalot/f1ac6277c9b224ebf4daada75a06294d) are one common use of scales!](https://cdn-images-1.medium.com/max/3728/1*c2dJV4ZNJGdWdWNV3AVPxQ.png)*[colors](http://blockbuilder.org/enjalot/f1ac6277c9b224ebf4daada75a06294d) are one common use of scales!*

Some of the most fundamental tools in the d3 tool belt are scales. Start with the [Introducing d3-scale](https://medium.com/@mbostock/introducing-d3-scale-61980c51545f) post by Mike Bostock to get an overview of what they are and how to use them. No matter what kind of visualization you do, you will likely use at least one kind of scale to make it happen.

### d3-shape

![A [streamgraph](http://bl.ocks.org/mbostock/582915), thanks to SVG paths!](https://cdn-images-1.medium.com/max/3828/1*6HpzsxMbWhLgTbAZNpf9Kg.png)*A [streamgraph](http://bl.ocks.org/mbostock/582915), thanks to SVG paths!*

SVG paths are pretty intense ([see this thorough overview](https://css-tricks.com/svg-path-syntax-illustrated-guide/)), so [d3-shape](https://github.com/d3/d3-shape#d3-shape) includes functions that will make them easier to create and manipulate for some use cases. Read Mike’s [Introducing d3-shape](https://medium.com/@mbostock/introducing-d3-shape-73f8367e6d12) article to get an overview of what it offers and how to get started using them. d3-shape can also help you render things like lines, areas and arbitrary paths to Canvas with just one extra line of code!

### d3-selection

![the [General Update Pattern](https://bl.ocks.org/mbostock/3808234)](https://cdn-images-1.medium.com/max/2000/1*-qbObwrIKYG3_bKpZBaUgw.gif)*the [General Update Pattern](https://bl.ocks.org/mbostock/3808234)*

One of the hardest parts of d3 to learn is the selection system, also referred to as the [General Update Pattern](https://bl.ocks.org/mbostock/3808218). It took me months of banging my head against my desk to internalize it, but don’t let that scare you! You don’t actually need to be a master of selections to get a ton of cool stuff done. When you are ready to give it a shot, you can start with the [d3-selections](https://github.com/d3/d3-selection#d3-selection) README and be sure to click on the links like [Thinking in Joins](https://bost.ocks.org/mike/join/).

### d3-collection

![d3-nest makes it easy to [group similar things together](https://bl.ocks.org/mbostock/9490313)](https://cdn-images-1.medium.com/max/3808/1*jcnYXBvv1W033jfwFOrS1A.png)*d3-nest makes it easy to [group similar things together](https://bl.ocks.org/mbostock/9490313)*

Manipulating data is an incredibly important part of visualizing it. This can often be the hardest part to do depending on how good your data is and how well you understand it. Having more tools that make working with the data by reshaping it, slicing and dicing or aggregating it can really help. To this end I recommend getting familiar with [d3-collection](https://github.com/d3/d3-collection/blob/master/README.md#d3-collection), particularly the [nest](https://github.com/d3/d3-collection/blob/master/README.md#nests) function.

### d3-hierarchy

![a [Treemap](https://bl.ocks.org/mbostock/8fe6fa6ed1fa976e5dd76cfa4d816fec) is easy to layout thanks to d3-hierarchy](https://cdn-images-1.medium.com/max/2580/1*1CszAZA3t5oMlTOMFchzSg.png)*a [Treemap](https://bl.ocks.org/mbostock/8fe6fa6ed1fa976e5dd76cfa4d816fec) is easy to layout thanks to d3-hierarchy*

Continuing the theme of working with data, the key part to many visualizations is laying out your visual representations based on your data’s structure. Some common functions for helping you do this are found in [d3-hierarchy](https://github.com/d3/d3-hierarchy#d3-hierarchy) for making things like trees, treemaps or circle packs.

### d3-zoom

![[zooming is fun](https://bl.ocks.org/mbostock/b783fbb2e673561d214e09c7fb5cedee)!](https://cdn-images-1.medium.com/max/2000/1*R96BnzJUPFSzTzWoxIBIAw.gif)*[zooming is fun](https://bl.ocks.org/mbostock/b783fbb2e673561d214e09c7fb5cedee)!*

A common interaction behavior you may want to add to your visualization is zooming. Mike has [a series of examples](http://blockbuilder.org/search#text=zoom;user=mbostock;d3version=v4) that show various ways of adding zoom to your visualization with [d3-zoom](https://github.com/d3/d3-zoom#d3-zoom). Check out zoom’s close cousin [d3-drag](https://github.com/d3/d3-drag#d3-drag) for some additional interaction help!

### d3-force

![[sparse matrices](https://bl.ocks.org/syntagmatic/75c5ca501200b0cf7a5958b4e404f777), amirite?](https://cdn-images-1.medium.com/max/2000/1*MiNLItcbqWuZP5y6DHVEsg.gif)*[sparse matrices](https://bl.ocks.org/syntagmatic/75c5ca501200b0cf7a5958b4e404f777), amirite?*

One of d3’s capabilities that inspires a lot of people is the force layout. It’s pretty easy to use but definitely difficult to master. Just don’t let it pull you to the dark side! See [d3-force](https://github.com/d3/d3-force#d3-force) for more details.

## Search!

One final tip you can use for any API function is to use [BlockBuilder’s search](http://blockbuilder.org/search) capability. You can also limit your searches to examples that use d3 v4!

![look at all those blocks!](https://cdn-images-1.medium.com/max/2000/1*AAKuY9w1DNMIq0oBqTe3KA.gif)*look at all those blocks!*

## Community

![Welcome to the [Blockiverse](http://bl.ocks.org/micahstubbs/b35f2560f4205570b3328d1b40de0c6c)](https://cdn-images-1.medium.com/max/2824/1*m3WWkrnN1pr6V-pFZgK8KQ.png)*Welcome to the [Blockiverse](http://bl.ocks.org/micahstubbs/b35f2560f4205570b3328d1b40de0c6c)*

Connect with like-minded folks! We have a vibrant online community in the [d3.js slack channel](https://d3-slackin.herokuapp.com/). Find a meetup near you, the SF Bay Area has [one of the biggest groups](https://www.meetup.com/Bay-Area-d3-User-Group/), but you can probably find some [groups near you](https://www.meetup.com/topics/d3-js/). If you’re really dedicated, come to the [annual d3.unconf](http://visfest.com) held in SF every fall!

![how I see the d3 community and the many learning curves one encounters on their journey](https://cdn-images-1.medium.com/max/5100/1*gybGRNFfU-ZBYtK3D1qGYA.png)*how I see the d3 community and the many learning curves one encounters on their journey*

*Many thanks to [Erik Hazzard](https://twitter.com/erikhazzard) for editing and helping shape this post. Thanks to [Kai Chang](https://twitter.com/syntagmatic) for his suggestions in this post but more so for helping shape the d3 community. Thanks to #teaching-d3 in the [d3js Slack](http://d3-slackin.herokuapp.com), especially [Sebastian](https://twitter.com/dashingd3js) and [John](https://twitter.com/JFSIII) for their feedback. Of course Infinite thanks to [Mike Bostock](https://twitter.com/mbostock) for creating the playground we all get to work in!*
