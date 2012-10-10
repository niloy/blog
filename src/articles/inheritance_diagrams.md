<!--
title: Creating Inheritance Diagrams for better documentation
-->
#Creating Inheritance Diagrams for better documentation

A typical large scale software is often broken using classes. These classes
follow complex inheritance patterns. Understanding the inheritance of classes
gives a better understanding of the software. So in this post today, we will
explore how to automatically create *Inheritance diagrams* from the code
directly.

For the demonstration, lets pick [THREE.js](https://github.com/mrdoob/three.js/)
which is a popular JavaScript library for creating 3D graphics in the browser.
Every piece of functionality in the library has been neatly wrapped in classes.

The first thing we need to do is download the source code. THREE.js already
provides production ready code [here](https://raw.github.com/mrdoob/three.js/master/build/three.min.js).
This single file contains all the classes in the project. This is especially
helpful because we don’t need to iterate over multiple folders to look for
class definitions.

In THREE.js, all inheritance is done using `Object.create`. For example,

    THREE.Camera.prototype=Object.create(THREE.Object3D.prototype)
    
This means, the class **Camera** inherits from **Object3D**. We can use **grep**
and find out all such statements.

    grep -o "THREE\.[^.]*\.prototype=Object\.create([^)]*)" three.min.js
    
The `-o` tells grep to only display matching portion instead of full line.
Executing the command above generates output like this:

    THREE.Camera.prototype=Object.create(THREE.Object3D.prototype)
    THREE.OrthographicCamera.prototype=Object.create(THREE.Camera.prototype)
    THREE.PerspectiveCamera.prototype=Object.create(THREE.Camera.prototype)
    THREE.Light.prototype=Object.create(THREE.Object3D.prototype)
    THREE.AmbientLight.prototype=Object.create(THREE.Light.prototype)
    THREE.DirectionalLight.prototype=Object.create(THREE.Light.prototype)
    THREE.HemisphereLight.prototype=Object.create(THREE.Light.prototype)
    THREE.PointLight.prototype=Object.create(THREE.Light.prototype)
    THREE.SpotLight.prototype=Object.create(THREE.Light.prototype)
    THREE.BinaryLoader.prototype=Object.create(THREE.Loader.prototype)
    THREE.JSONLoader.prototype=Object.create(THREE.Loader.prototype)
    ...
    
We can pipe this output to the following **sed** command:

    sed 's/THREE\.\([^.]*\)\.prototype=Object\.create(THREE\.\([^.]*\)\.prototype)/\t\2 -> \1/'
    
Which will generate the following output:

    Object3D -> Camera
	Camera -> OrthographicCamera
	Camera -> PerspectiveCamera
	Object3D -> Light
	Light -> AmbientLight
	Light -> DirectionalLight
	Light -> HemisphereLight
	Light -> PointLight
	Light -> SpotLight
	Loader -> BinaryLoader
	Loader -> JSONLoader
	...
	
We need to generate this particular format because this is understood by the
[graphviz](http://www.graphviz.org/) library. **graphviz** is a Graph Visualization
Software that can create graph/tree images from input text files. On Ubuntu,
installing it is as simple as:

    sudo apt-get install graphviz
    
>Note: `dot` is the actual name of the executable that generates the graph. But,
>it comes under the *graphviz* package.

The final input to `dot` should be this:

    digraph tree {
        Object3D -> Camera
        Camera -> OrthographicCamera
        Camera -> PerspectiveCamera
        Object3D -> Light
        Light -> AmbientLight
        Light -> DirectionalLight
        Light -> HemisphereLight
        Light -> PointLight
        Light -> SpotLight
        Loader -> BinaryLoader
        Loader -> JSONLoader
    }

The code above is also known as the [DOT language](http://en.wikipedia.org/wiki/DOT_language).

Assuming the dot commands is stored in a file called `tree.gv`, execute the
following command:

    dot -Tsvg -othree.tree.svg tree.gv
    
This command will generate the tree output in SVG format in the file
[three.tree.svg](three.tree.svg).

Putting it all together in a shell script:

    echo "digraph tree {" > /tmp/tree.gv
    grep -o "THREE\.[^.]*\.prototype=Object\.create([^)]*)" three.min.js|\
    sed 's/THREE\.\([^.]*\)\.prototype=Object\.create(THREE\.\([^.]*\)\.prototype)/\t\2 -> \1/'\
    >>/tmp/tree.gv
    echo "}" >> /tmp/tree.gv
    dot -Tsvg -othree.tree.svg /tmp/tree.gv
    
And you are done. Ofcourse, this idea is not limited to inheritance diagrams. One
can plot a graph of various function calls being made by a certain class. Or
the entire site diagram where each node is a page and each edge represents a
hyperlink. The possibilities are really endless!

