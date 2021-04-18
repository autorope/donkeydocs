## Tub

This is the standard donkey data store and it is modeled after the [ROSBAG](http://wiki.ros.org/rosbag).
All sensor data is stored in a `Tub`. 

### Accepted Types

The following datatypes are supported. 

* `str`
* `int`
* `float` / `np.float`
* `image_array`s and `array`s (`np.ndarray`)
* `image` (jpeg / png)

The `Tub` is an append only format, that is optimized for reads (to speed up training models). 
It maintains indexes for records, and uses memory mapped files.

The `Tub` exposes an `Iterator` that can be used to read records. These iterators can be further used by `Pipeline`s to do arbitrary transformations of data prior to training (for data augumentation).

### Example

```python
from donkeycar.parts.tub_v2 import Tub

# Here we define records that have a single `input` of type `int`.
inputs = ['input']
types = ['int']
tub = Tub(path, inputs, types)

```
