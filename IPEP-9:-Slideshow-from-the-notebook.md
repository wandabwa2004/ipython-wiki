# IPEP 8 - Slideshow from the notebook.

## Introduction

The aim of this IPEP is to discuss about some features of the implemented *live* slideshow_mode by @Carreau and the new *static* one pull requested by @damianavila as a part of nbconvert utilities.

As @ellisonbg suggested, there were a lot of places were the discussion was active, so I am writing this IPEP to collect all this ideas and centralized the place to think about this interesting feature.

Up to now, we have two implementation being developed to generate slideshow views from a notebook:

### *Live* slideshow

The first one has been developed by @Carreau, and I called *live* slideshow, because **is** an active notebook with a different template to provide the feel like a slideshow. To delimit each slide, the last implementation use metadata attributes for each cell.

There are several advantages and constraints about this *live* mode:

#### Advantages:

* You have a slideshow with editing capabilities and this interactivity can be useful presenting some ideas.
* Because you use metadata delimiters, you do not have to rewrite the notebook to be slideshow-friendly.
* You can put all the information you want in the slides because you have the possiblity to get this content with a lateral bar as a *normal* webpage.

#### Constraints:

* The *live* slideshow is a notebook currently communicating with one IPython server, so you need IPython to present it. This can be awkward when you want to share your slideshow with someone else.
* You do not have a easy way to get a pdf view from the slideshow.
* You get a notebook style in you slides that can be great for some audience but boring for others (and all of you know that you have to get the people awake... he he).

### *Static* slideshow

The second one has been developed by @damianavila (hey, I hate talking in third person of myself... he he), and I called *static* slideshow, because this is not a notebook, it is just html + css + js parsing the notebook contents and representing them in a nice way. The last implementation is also using metadata attributes to delimit the slides. In this case, you have new_section (new horizontal slide), new_subsection (new nested/vertical slide) and new_fragment (new fragment in the current slide) - thanks to @Carrieu for support this format (derived from reveal.js library) in his metaui branch. This implementation is currently a PR for nbconvert. You can see an example slideshow from a notebook [here](http://www.slideviper.oquanta.info/nbcreveal/example_slide_slides.html)

#### Advantages:

* This is a *static* presentation, so you do not need IPython running to use it.
* Because you use metadata delimiters, you do not have to rewrite the notebook to be slideshow-friendly.
* You can upload as a webpage to a free host, such as github pages, dropbox, even it could be part of nbviewer
to render slideshow from uploaded ipy notebooks.
* You get all the slyling (very nice) and power (lot of plugins) of reveal.js library.
* You can also get a pdf from the slides to share and publish.

#### Constraints:

* You do not have editing in *live*, maybe necessary for some audience.
* You can not put all you want in one slide, you have a limited space to put content. If you want to put more content, you have to add another slide.
* Until now, there is no support for markdown in fragments, so inside fragments you can only put plain text (I open an issue in reveal.js proyect, I hope we can have soon this support). 

As you can see, with @Carreau and @ellisonbg we have proposed to use the same type of delimiter for the slides (metadata), so we can create a notebook to show as *live* slideshow, that can be *nbconverted* to a *static* one based in reveal without minimal (we hope, any) changes.

#### Metadata structure. 

As those are metadata all attributes are optional and implementation relying on it should check their existance.

```
metadata :{
    slideshow :{
        new_section: [true|false|undefined],
        new_subsection: [true|false|undefined],
        new_fragment: [true|false|undefined]
    }
}
```

The Proposed UI to change those value are label+checkbox representing the state of current metadata.
Ticked checkbox for `true` value of corresponding metadata. Unticked for `false`,`undefined`.

Respective Labels are `New Section`,`New Subsection`, `New Fragment`.

Of course the UI can be change independently from the metadata.

#### Structure behind the static slideshow

This proposed structure is in accordance with reveal.js structure to easily render as html slideshow.
All the cells, whatever their contents can be metadata-applied. 

* **New_section** (alternative name: **new_slide**) is the fundamental unit of content: you have to use it on a cell that begins new units of content (that will appear on the screen at one time, ignoring transitions within the slides). You can add new cells to this slide, but they can only be marked as **New_Fragment** or **None** (see them later).
* **New_subsection** (alternative name: **new_second**) begins a new group of cells that, logically, belong together. This have to be applied to all the cells of the group, but the first cell also must have **New_section** (**new_slide**) applied. In reveal.js, these are vertically oriented groups of slides.
**Note**: In the current implementation, we can detect if the user did not apply **New_section** (**new_slide**) to the first cell and "make" it for him. 
* **New_fragment** are used to highlight individual elements on a slide. Every element with the **New_fragment** will be stepped through before moving on to the next slide. You can applied **New_fragment** to any cell to highlight it.
* When you do not applied any label, we assumed this cell is **None**, so these are the mainly the "content" of the slides and can be part of a **New_section** (**new_slide**) or **New_subsection** (**new_second**).

Ok, I have opened a issue to follow the discussion [here](https://github.com/ipython/ipython/issues/2680)

I hope you join us!

Damián.

NOTE:

For more detailed information, can see relevant links about the discussion in several issues listed below:

#### Main references

##### In IPython main repository:
https://github.com/ipython/ipython/pull/2127 # First implementation of *live* slideshow mode to demo js toolbar by @Carreau
https://github.com/ipython/ipython/pull/2193 # Brainstorming for *live* slideshow extension and implementation by @Carreau
https://github.com/ipython/ipython/pull/2333 # Discussion about user interface for metadata and implementation by @Carreau

##### In nbconvert:
https://github.com/ipython/nbconvert/pull/63 # New *reveal* option in nbconvert to get a *static* html-based slideshow powered by reveal.js. Implementation by @damianavila

##### Minor references

https://github.com/ipython/ipython/issues/977
https://github.com/ipython/ipython/pull/2199
https://github.com/ipython/ipython/pull/2518
https://github.com/ipython/ipython/pull/2549