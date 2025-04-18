# CEC Dynamic Scene Graph

A dynamic scene graph! This package makes it easy to construct a dynamic scene graph that support partial observability -- objects can move, and not all objects must be visible at once. The package defines the object structure and resolves which object is seen given an observation of its global coordinates.

The package is designed to be as easy to use as possible. It had no dependencies and can easily fit into your perception pipeline.

Maintained by Jack Kolb (jack@jackkolb.com).

## Install

We use `setuptools` to install the package for accessibility to all of your projects and to reduce clutter in your project codebase.

Clone this repository:

`git clone https://github.com/gt-cec/cec-dsg`

Install via `pip`:

`pip install -e cec-dsg`

And that's it!

## Usage

The `DSG` class contains all functionality for accessing and updating the scene graph.

```
import cec_dsg

# create a DSG object, objects are the initial objects, same_location_threshold is the threshold for considering an object in the same location
scene = dsg.DSG(objects=[], same_location_threshold=4)
```

Objects (dictionaries of `class`, `x`, `y`, `z` fields) can be added to the scene graph:
```
# define an object, this could come from perception or SLAM
book = {
        "class": "book",
        "x": 7,
        "y": 6,
        "z": 1.0
    }

# add the object to the scene graph
scene.add_object(object_dict=book)
```

Alternatively, the scene graph can be initialized by adding a list of objects at once:
```
objects = [
    {
        "class": "book",
        "x": 7,
        "y": 6,
        "z": 1.0
    },
    {
        "class": "table",
        "x": 6,
        "y": 5,
        "z": 0.8
    }
]

scene.initialize_scene(objects, verbose=True)
```

When objects are observed, update the scene graph:
```
# define a list of observed objects
seen_objects = [
        {
            "class": "book",
            "x": 7.5,
            "y": 5.5,
            "z": 1.4
        }
    ]

# update the scene graph
scene.update(seen_objects)
```

To remove an object, use its object ID:
```
# knowing the book we added is O1, remove it
scene.remove_object("O1")
```

We can get information about the scene graph for use in other system components:
```
# get the number of objects in the scene graph
num_objects = scene.count()

# get all objects organized by class
objects = scene.get_objects_by_class()  # {"books": [{...}, {...}, ...]}

# get the objects organized by object ID
objects = scene.get_objects_bt_id()  # {"O1": {...}, "O2": {...}}
```

## Issues

The resolver can fail if it is given more objects of a class than exist in the scene. Until object detection confidence is implemented, filter by confidence before sending the object updates to the scene graph.

The resolver will fail if it is given a class that does not exist in the scene. Filter out invalid object classes before sending the object updates to the scene graph.

