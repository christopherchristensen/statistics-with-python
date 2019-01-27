# Matplotlib

Matplotlib is a multiplatform data visualization library built on NumPy arrays.
It supports different graphic backends and output styles, and works on
virtually any platform. Some projects, including Pandas, offer wrappers around
the API of Matplotlib. It is, however, still useful to know how to deal
directly with Matplotlib.

Conventionally, Matplotlib is imported as follows:

```python
>>> import matplotlib as mpl
>>> import matplotlib.pyplot as plt
```

The plot style can be set on the `plt` object:

```python
>>> plt.style.use('classic')
```

Depending on the context, there are different ways of opening the plots for
display.

From a script, the method `plt.show()` opens all figures plotted so far:

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
plt.plot(x, np.sin(x))
plt.plot(x, np.cos(x))

plt.show()
```

The method `plt.show()` must onle be used once per script or session.

Plots created in a IPython shell can be displayed automatically by calling the
`%matplotlib` magic command before calling methods on the `plt` object. The
plot will be displayed in a separate window. The method `plt.draw()` forces the
output to be updated.

```python
>>> import matplotlib as mpl
>>> import matplotlib.pyplot as plt
>>> import numpy as np

>>> %matplotlib
Using matplotlib backend: Qt5Agg

>>> x = np.linspace(0, 10, 100)
>>> plt.plot(x, np.sin(x))
```

From within a Jupyter Notebook, there are two options to display plots:

1. `%matplotlib inline`: display plots as static images
2. `%matplotlib notebook`: display interactive plots

The latter option will draw every plot output in the most recent figure, which
can be created using the `plt.figure()` method:

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)

%matplotlib notebook

plt.figure()
plt.plot(x, np.sin(x))
plt.plot(x, np.cos(x))
```

A figure object can be saved using its `savefig()` method, which requires a
file name. Notice that the `plot()` method only draws into the most recent
figure object created, if the magic command `%matplotlib` hasn't been used
before:

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np

fig = plt.figure()
x = np.linspace(0, 10, 100)
plt.plot(x, np.sin(x))
plt.plot(x, np.cos(x))
fig.savefig('sin-x-cos-x.png')
```

![Plot of `sin(x)` and `cos(x)`](plots/sin-x-cos-x.png){width=426px}

An image--no longer a plot!--can be loaded using IPython's `Image` object:

```python
>>> from IPython.display import Image, display
>>> img = Image('sin-x-cos-x.png')
>>> display(img)
```

For both saving and loading, the file format is inferred from the file's
extension. The formats supported by the graphics backend in use can be
retrieved as a dictionary from a `figure` object:

```python
>>> import matplotlib as mpl
>>> import matplotlib.pyplot as plt

>>> fig = plt.figure()
>>> fig.canvas.get_supported_filetypes()
{'ps': 'Postscript',
 'eps': 'Encapsulated Postscript',
 'pdf': 'Portable Document Format',
 'pgf': 'PGF code for LaTeX',
 'png': 'Portable Network Graphics',
 'raw': 'Raw RGBA bitmap',
 'rgba': 'Raw RGBA bitmap',
 'svg': 'Scalable Vector Graphics',
 'svgz': 'Scalable Vector Graphics'}
```

## Interfaces: MATLAB-style and Object Oriented

Matplotlib started out as a Python alternative for MATLAB. The `plt` object
represents the stateful interface known to MATLAB users. Plots created on the
`plt` object are drawn to the figure and axes objects that have been created
most recently.

In this example, two subplots on a single figure are created:

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
plt.figure() # create a new figure
plt.subplot(2, 1, 1) # (row, column, panel): first panel on a 2*1 field
plt.plot(x, np.sin(x)) # plot to the first subplot
plt.subplot(2, 1, 2) # second panel on the same 2*1 field
plt.plot(x, np.cos(x)) # plot to the second subplot
plt.show()
```

![MATLAB-style interface: Subplots](plots/matlab-style-1.png){width=426px}

It is possible to plot on other figures/axes than the current active, but only
if their references have been retrieved and stored using `plt.gcf()` (get
current figure) and `plt.gca()` (get current axes):

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
plt.figure()
plt.subplot(2, 1, 1)
plt.plot(x, np.sin(x))
first = plt.gca() # store reference to first aces
plt.subplot(2, 1, 2)
plt.plot(x, np.cos(x))
first.plot(x, np.cos(x)) # also draw cosine on first axes
plt.show()
```

![MATLAB-style interface: Draw to "inactive" Axes](plots/matlab-style-2.png){width=426px}

"Going back" is not possible if one fails to store the such references,
especially in an interactive session. The object-oriented interface of
Matplotlib doesn't rely on a _current state_, but requires the user to always
explicitly refer to the figure/axes to be dealt with:

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
fig, ax = plt.subplots(2)
ax[0].plot(x, np.sin(x))
ax[1].plot(x, np.cos(x))
ax[0].plot(x, np.cos(x))
plt.show()
```

The choice between the two interfaces is mostly a matter of preference for
simple tasks. More complicated plots, however, do require the object-oriented
approach.

## Line Plots

Simple functions of the form `y=f(x)` can be visualized using line plots. The
following examples require this boilerplate code:

```python
import matplotlib.pyplot as plt
import numpy as np

%matplotlib inline

plt.style.use('seaborn-whitegrid') # simple style

x = np.linspace(0, 10, 100) # 100 points in range 0..10
```

A figure, implemented by `plt.Figure`, contains all the graphics objects, like
text, labels -- and the axes. A axes, implemented by `plt.Aces`, is a bounding
box with ticks and labels, which contains the plotted lines. Conventionally,
the objects are called `fig` and `ax`:

```python
fig = plt.figure()
ax = plt.axes()
```

The sine function of the `x` values computed before can be drawn using the aces
`plot()` method:

```python
ax.plot(x, np.sin(x)) # plot x and y=sin(x)
```

The MATLAB-style interface can be used alongsinde, plotting to the figure/axes
used most recently:

```python
plt.plot(x, np.cos(x)) # plot x and y=cos(x)
```

The lines get a color assigned automatically from a predefined set. The colors
can also be assigned manually using the `color` keyword of the `plot()` method.
The following options are supported:

- HTML color name: `blue`, `green`, `fuchsia` etc. (common HTML color names)
- RGB/CMYK short code: `r`, `g`, `b`, `c`, `m`, `y`, `k`
- Grayscale value: floating point number between `0` (black) and `1` (white)
- RGB hex code: `#ff00aa`, `#efefef`
- RGB tuple with floating point numbers between `0` and `1`: `(0.1, 0.75, 0.66)`

The line style can be adjusted using the `linestyle` keyword. The following
options are supported, both having a short and a long form:

- `-`/`solid`
- `--`/`dashed`
- `-.`/`dashdot`
- `:`/`dotted`

```python
ax.plot(x, np.sin(x-0), color='fuchsia', linestyle='-')
ax.plot(x, np.sin(x-1), color='m', linestyle='--')
ax.plot(x, np.sin(x-2), color='0.25', linestyle='-.')
ax.plot(x, np.sin(x-3), color='#0a123b', linestyle=':')
ax.plot(x, np.sin(x-4), color=(0.1, 0.75, 0.66))
```

![Line Colors and Styles](plots/line-color-style.png){width=426px}

The MATLAB-style interface `plt.plot()` accepts a shorthand style indicator as
a third non-keyword argument, combining a line style with a a RGB/CMYK color
code:

```python
plt.plot(x, np.sin(x-5), ':y') # dotted yellow line
```

## Limits, Labels, Legends

The axes limits can be set using the `plt.xlim()` and `plt.ylim()` function
(MATLAB-style) or using the `ax.set_xlim()` and `ax.set_ylim()` method
(OO-style) by passing a lower and a upper bound:

```
plt.xlim(-5, 5)
plt.ylim(-1, 1)
```

A plot can be flipped along both axis by passing the arguments in reverse
order (using an `axes` object here):

```
ax.set_xlim(5, -5)
ax.set_ylim(1, -1)
```

The `plt.axis()` method allows to set both ranges at once by providing a list
of the form `[xmin, xmax, ymin, ymax]`:

```python
plt.axis([-5, 5, -1, 1])
```

The ranges can be set automatically to just fit in the plot by using the
`'tight'` parameter:

```python
plt.axis('tight')
```

The `'equal'` parameter makes sure the plot fits in and that the x and y axis
are scaled equally:

```python
plt.axis('equal')
```

The axes object supports the same method: `ax.axis()`.

Both axis and the plot as a whole can be labeled using the `plt.xlabel()`,
`plt.ylabel()` and `plt.title()` function (MATLAB-style) or the
`ax.set_xlabel()`, `ax.set_ylabel()`, `ax.set_title()` method (OO-style):

```python
plt.xlabel('x')
plt.ylabel('y=sin(x)')
plt.title('A Sine Curve')

ax.set_xlabel('x')
ax.set_ylabel('y=cos(x)')
ax.set_title('A Cosine Curve')
```

Lines with different styles and colors can be labeled with a legend by calling
the `plt.legend()` function or the `ax.legend()` method, which requires the
individual plots (as opposed to its axis) to be labeled with the `plot()` call
(keyword `label`):

```python
plt.plot(x, np.sin(x), '-g', label='sin(x)')
plt.plot(x, np.cos(x), ':b', label='cos(x)')
plt.legend()

ax.plot(x, np.sin(x), color='green', linestyle='-', label='sin(x)')
ax.plot(x, np.cos(x), color='blue', linestyle=':', label='cos(x)')
ax.legend()
```

The `ax.set()` method is a convenient interface for setting limits (using
tuples), labels and a title all at once:

```python
ax.set(xlim=(0, 10), ylim=(-1, 1),
       xlabel='x', ylabel='sin(x)',
       title='A Sine Curve')
```

Bringing it all together (in a script):

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)

ax = plt.axes()
ax.set(xlim=(0, 10), ylim=(-1, 1),
       xlabel='x', ylabel='y',
       title='Sine and Cosine')

ax.plot(x, np.sin(x), color='green', linestyle='-', label='sin(x)')
ax.plot(x, np.cos(x), color='blue', linestyle=':', label='cos(x)')
ax.legend()

plt.show()
```

![Limits, Labels, Legends](plots/limits-labels-legends.png){width=426px}

## Scatter Plots

Scatter plots represetn the data points individually instead of joining them
with a line. The `plt.plot()` function is capable of producing scatter plots,
if the third argumetnis a character representing an according symbol, such as
`'o'`, `'.'`, `','`, `'x'`, `'+'`, `'v'`, `'^'`, `'<'`, `'>'`, `'s'`, `'d'`:

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 50)
plt.plot(x, np.sin(x), 'o', label='sin(x)')
plt.plot(x, np.cos(x), 'x', label='cos(x)')

plt.legend()
plt.show()
```

![Sine and Cosine curve as a scatter plot](plots/scatter-sin-cos.png){width=426px}

The dots can be connected when combining the style parameter with a line style:

```python
plt.plot(x, np.sin(x), 'o-c', label='sin(x)') # dots & solid cyan line
plt.plot(x, np.cos(x), 'x:m', label='cos(x)') # crosses & dotted magenta line
```

![Points connected with a line](plots/scatter-point-line.png){width=426px}

The lines and markers (points) can be further specified using the following
arguments of the `plt.plot()` function:

- `markersize`
- `markerfacecolor`
- `markeredgecolor`
- `markeredgewidth`
- `linewidth`

```python
plt.plot(x, np.sin(x), 'o-c', markersize=10, markerfacecolor='blue',
         markeredgecolor='white', markeredgewidth=2, linewidth=3)
```

![Marker options](plots/scatter-marker-options.png){width=426px}

The function `plt.scatter()` can set the individual properties of each point by
passing a list instead of a single value as the size and color parameters (`s`
and `c`):

```python
n_points = 100
rng = np.random.RandomState(0)
x = rng.randn(n_points)
y = rng.randn(n_points)

colors = rng.randn(n_points)
sizes = 500 * rng.randn(n_points)

plt.scatter(x, y, c=colors, s=sizes, alpha=0.3)

plt.colorbar()
plt.show()
```

![Scatter plot with individual marker sizes and colors](plots/scatter-4d.png){width=426px}

This is useful for visualizing multi-dimensional data (four dimensions: x and y
value, color and size).

Because `plt.scatter()` figures out the rendering for each individual point
separately, it can be slower than `plt.plot()`, especially when dealing with
big data sets. If all the scatter points are to be drawn alike, `plt.plot()`
should be prefered to `plt.scatter()`.

The OO-style interface (`ax.plot()`, `ax.scatter()`) works with the same
parameters.

## Visualizing Errors

In many applications, reporting the range of possible error is just as
important as reporting the value itself.

For discrete values, Matplotlib can plot error bars using the `plt.errorbar()`
function. The error range, either on the x- or y-axis, can be set using the
parameter `xerr` or `yerr`, respectively. The `fmt` parameter accepts a format
specifier consisting of style and color code:

```python
rng = np.random.RandomState(0)
points = 20
dy = 0.5

x = np.linspace(0, 10, points)
err = dy * rng.randn(points)
y = np.sin(x) + err

plt.errorbar(x, y, yerr=dy, fmt='.')
plt.show()
```

![Vertical Error Bars](plots/errorbar-1.png){width=426px}

The error bar can be further fine-tuned by specifying the `ecolor` (color of
the bar), the `elinewidth` (the width of the error bar) and the `capsize` (the
size of the ticks orthogonal to the error bar) parameters:

```python
plt.errorbar(x, y, yerr=dy, fmt='.', ecolor='#cccccc', elinewidth=2, capsize=5)
```

![Customized Error Bars](plots/errorbar-2.png){width=426px}

The error of continuous quantities can be indicated by filling a area around
the graph displaying the values. This can be achieved by using a combination of
the `plt.plot` (indicating the values) and the `plt.fill_between` (indicating
the area of error) function.

```python
rng = np.random.RandomState(0)
points = 100
dy = 0.25

x = np.linspace(0, 10, points)
err = dy * rng.randn(points)
y = np.sin(x) + err

plt.plot(x, y)
plt.fill_between(x, y-dy, y+dy, color='#cccccc', alpha=.5)
plt.show()
```

The second argument (`y-dy`) is the lower, the third argument (`y+dy`) the
upper bound of the error area.

![Error Area for Continuous Values](plots/error-area.png){width=426px}

The methods `errorbar` and `fill_between` are also available in the axes'
OO-style interface:

```python
fig, ax = plt.subplots(2)
ax[0].errorbar(x, y, yerr=err, fmt='.')
ax[1].plot(x, y)
ax[1].fill_between(x, y-dy, y+dy, color='#cccccc', alpha=.5)
plt.show()
```

![Error Bar and Area Combined](plots/errors-combined.png){width=426px}