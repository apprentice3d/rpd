---
title: Introduction
id: introduction
---

<div id="logo-patch-target">
    <img id="large-logo" src="./rpd.svg" width="140" height="140" />
    <div id="rpd-logo-patch"></div>
</div>

<!-- TODO SVG icon patch: -->
<!-- sun and moon -->
<!-- circle particles -->
<!-- logo color shift -->
<!-- https://www.instagram.com/p/BAH_8hJsCZ4/ ? -->

### What is RPD?

RPD is the abbreviation for _Reactive Patch Development_...

...or, actually, whatever you decide. It is the library which brings node-based user interfaces to the modern web, in full their power (if you know how to use it) and in a very elegant and minimalistic way. _Node-based_ is something like the thing you (probably) see above, (almost) nothing to do with [node.js][node-js]. If you are wondering yet, what that means, _Node-based_ interface is the one where man may visually connect different low-level components using their inputs and outputs and observe the result in real time, take PureData, QuartzComposer, VVVV, NodeBox, Reaktor etc. for example.

<!-- TODO: video or some example patch, processing patch from vimeo? -->

_Elegancy_ is achieved both with providing you a very simple API for building these powerful things, and (thanks to the reactive streams, powered by [Kefir.js library][kefir]) pure functional approach in the core, so it's easy for you to operate with sequences of data over time in any way you want, and also every action performed, (such as adding a node, or connecting something, or sending a value) is atomic, so it can easily be rolled back or stored in, and so restored from, some file.

<!-- an example of defining simple node (and channel?) type and connecting it to a patch,
show streams and simple values -->

_Minimalism_ is another goal of this library, which implies the RPD library size
is kept as minimal as possible, so your customer may load the interface you created using 3G internet or wi-fi limited to some very low speed. Don't ask me why may it happen, it still happens everywhere. Minimalism is here not in paranoid amounts, though&mdash;if feature requires a bit more code, or some task gets very complex with less code, we for sure will add some more code for the sake of simplicity.

The default configuration with [SVG renderer][renderer-comp-section] and [Quartz style][style-comp-section] included takes _11KB_ when compiled, minimized and gzipped! (30KB not gzipped). Though you also need [latest Kefir.js](roman-pominov), the only required dependency to make it work, which adds just ~10KB more, since [Kefir.js author][roman-pominov] also likes minimalism in code.

If you feel you that's you know everything and this library is definitely what you need (and it is!), you may either download the [version with default configuration][download-default] or go straight to [Building Section](./sections/building) to discover how easy it is to grab a code and configure a custom one. If you still feel unsafe, stay with me for a bit.

### Code Examples

<!-- TODO -->

### Terminology

First, we'll visit terms of node-based interfaces from high level (_Network_) to a low level (_Network_). If that's not comfortable for you, you may go from _Link_ to _Network_ in reverse direction.

<!-- TODO Image or interactive example (jsfiddle, run by click?) showing the network of simple patches -->

#### Network

_Network_ defines a system of Patches. At this level Patch may be considered as a complex procedure with several inputs and outputs and a Network is a program that uses these procedures.

Say, you define some complex functionality of a 3D vertex shader with a Patch. It has its inputs such as 3D position, color and texture coordinate of a vertex and outputs as 2D coordinate and modified color. Then you may create another “root” Patch, which allows user to apply combinations of different vertex shaders to an imported object and so re-use the Shader Patch several times in different configurations. These two Patches (first one used several times and second one used once) form a simple Network.

<!-- TODO: diagram of a vertex shader network -->

So, some Node in one Patch may represent the inputs and outputs of another Patch's copy, running in the same Network.

#### Patch

_Patch_ stores a collection of Node instances and connections between them.
The resulting structure of Nodes and connections defines the way data flows in this Patch.

<!--TODO Two nodes connected, to show inlets and outlets (run by click?) -->

#### Node

_Nodes_ are building blocks which use connections to receive any data from one nodes, modify it, and send transformed data to another Nodes. Nodes may have zero or more inputs of different types, named _Inlets_ and zero or more outputs of different types, named _Outlets_. Nodes may have a body which may represent received data or even have some controls allowing user to change it though hidden inlets.

#### Outlet

_Outlet_ is a socket of a Node designed to send outgoing data. Outlet may be connected to an Inlet of another Node or to several Inlets of other Nodes.

Type of the Outlet determines which types of values it sends.

Outlet and Inlet types are called Channel Types.

#### Inlet

_Inlet_ is a socket of a Node designed to receive incoming data. Depending on configuration, it may accept only one connection from an Outlet or any number of connections from several Outlets.

Inlet may have a default value, so it sends it to the Node when the latter is added to a Patch.

Inlet may be hidden, so it won't be visible for user, but may receive data from inputs located in the body of the Node.

Inlet may be “cold”, in contrary to “hot” by default, so it won't trigger the modifying process, but save the last value that came to this Inlet.

Inlets may accept or deny values depending on their type. Type may specify a function which will transform data before it will be sent to the Node.

#### Link

_Link_ is what connects single Outlet to single Inlet. Always one to one. It may be disabled, so it will not deliver all the data which comes inside, but keep the connection.

<!-- TODO Nodes from different toolkits -->

#### Toolkit

_Toolkit_ is a group of Node and Channel Types lying in (preferrably, but not required) one namespace. For example, [Processing.js][processing-js] Toolkit may define Nodes and Channels which control the configuration of a Processing Sketch, use and convert data types specific to Processing and so on.

<!-- TODO SVG and HTML -->

#### Renderer

_Renderer_ is a system which determines the way current Patch model is rendered. For now, there are two Renderers: HTML and SVG, they render Patches to HTML or SVG tags correspondingly. For instance, HTML Renderer renders Link connections as `span` blocks with CSS borders and SVG Renderers just draws SVG `line` tags for the same purpose.

Also, Renderer determines where new node will be placed if position was not specified.

#### Node Type Renderer

_Node Type Renderer_ builds the body of the Node and may update its content when some incoming update triggered it. Also, it may send values from inner controls to a hidden Inlets of the Node.

There should be a separate Node Type Renderer for each way to render a node, such as HTML, SVG and so on. By default, if Node can't render itself in the requested way, it is rendered as an empty Node, yet having all the defined Inlets and Outlets.

#### Channel Type Renderer

_Channel Type Renderer_ builds the Inlet/Outlet value representation and also may add the editor to a Channel value.

Editor is an optional control which lets user override the value in the Inlet.

_NB: For the moment, value editors are only supported in HTML Renderer_

#### Style

_Style_ determines the look of the Patch, Node, Channel or a Link. While Renderer builds the outer structure, controls drag-n-drop and other logic, Style only determines the inner visual appearance of these elements.

#### I/O Module

_I/O Module_ records every action (or only some of the actions) performed to build the Network in some specific format, so it can optionally “replay” these actions in order, using this data.

<!-- All the updates inside the Network are based on purely functional code, so there are no actual data modifications performed, only signals are sent. Among other useful things, it allows to easily record and restore things. Your code could be imperative, if you decide, but you should not modify the Network structure if you plan to share the code with others. -->

[node-js]: http://nodejs.org
[kefir]: http://rpominov.github.io/kefir/
[roman-pominov]: http://rpominov.github.io
[processing-js]: http://p5js.org

[download-default]: TODO
[building-section]: ./sections/building.html
[renderer-comp-section]: ./sections/compilation.html#renderers
[style-comp-section]: ./sections/compilation.html#styles

<script defer src="./index-patches.js"></script>