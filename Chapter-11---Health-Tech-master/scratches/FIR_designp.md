% FIR filter design with Python and SciPy
% Matti Pastell
% 15th April 2013

# Introduction

This an example of a script that can be published using
[Pweave](http://mpastell.com/pweave). The script can be executed
normally using Python or published to HTML with Pweave
Text is written in markdown in lines starting with "`#'` " and code
is executed and results are included in the published document.
The concept is similar to
publishing documents with [MATLAB](http://mathworks.com) or using
stitch with [Knitr](http://http://yihui.name/knitr/demo/stitch/).

Notice that you don't need to define chunk options (see
[Pweave docs](http://mpastell.com/pweave/usage.html#code-chunk-options)
),
but you do need one line of whitespace between text and code.
If you want to define options you can do it on using a line starting with
`#+`. just before code e.g. `#+ term=True, caption='Fancy plots.'`.
If you're viewing the HTML version have a look at the
[source](FIR_design.py) to see the markup.

The code and text below comes mostly
from my blog post [FIR design with SciPy](http://mpastell.com/2010/01/18/fir-with-scipy/),
but I've updated it to reflect new features in SciPy.

# FIR Filter Design

We'll implement lowpass, highpass and ' bandpass FIR filters. If
you want to read more about DSP I highly recommend [The Scientist
and Engineer's Guide to Digital Signal
Processing](http://www.dspguide.com/) which is freely available
online.

## Functions for frequency, phase, impulse and step response

Let's first define functions to plot filter
properties.



```python
from pylab import *
import scipy.signal as signal

#Plot frequency and phase response
def mfreqz(b,a=1):
    w,h = signal.freqz(b,a)
    h_dB = 20 * log10 (abs(h))
    subplot(211)
    plot(w/max(w),h_dB)
    ylim(-150, 5)
    ylabel('Magnitude (db)')
    xlabel(r'Normalized Frequency (x$\pi$rad/sample)')
    title(r'Frequency response')
    subplot(212)
    h_Phase = unwrap(arctan2(imag(h),real(h)))
    plot(w/max(w),h_Phase)
    ylabel('Phase (radians)')
    xlabel(r'Normalized Frequency (x$\pi$rad/sample)')
    title(r'Phase response')
    _ = subplots_adjust(hspace=1)
    show()

#Plot step and impulse response
def impz(b,a=1):
    l = len(b)
    impulse = repeat(0.,l); impulse[0] =1.
    x = arange(0,l)
    response = signal.lfilter(b,a,impulse)
    subplot(211)
    stem(x, response)
    ylabel('Amplitude')
    xlabel(r'n (samples)')
    title(r'Impulse response')
    subplot(212)
    step = cumsum(response)
    stem(x, step)
    ylabel('Amplitude')
    xlabel(r'n (samples)')
    title(r'Step response')
    _ = subplots_adjust(hspace=1)
    show()
```

```
---------------------------------------------------------------------------ModuleNotFoundError
Traceback (most recent call last)<ipython-input-1-d556b261cb25> in
<module>()
      1 from pylab import *
----> 2 import scipy.signal as signal
      3
      4 #Plot frequency and phase response
      5 def mfreqz(b,a=1):
ModuleNotFoundError: No module named 'scipy'
```



## Lowpass FIR filter

Designing a lowpass FIR filter is very simple to do with SciPy, all you
need to do is to define the window length, cut off frequency and the
window.

The Hamming window is defined as:
$w(n) = \alpha - \beta\cos\frac{2\pi n}{N-1}$, where $\alpha=0.54$ and $\beta=0.46$

The next code chunk is executed in term mode, see the [Python script](FIR_design.py) for syntax.
Notice also that Pweave can now catch multiple figures/code chunk.



```python
n = 61
a = signal.firwin(n, cutoff = 0.3, window = "hamming")
```

```
---------------------------------------------------------------------------NameError
Traceback (most recent call last)<ipython-input-1-0edf555780c5> in
<module>()
----> 1 a = signal.firwin(n, cutoff = 0.3, window = "hamming")
NameError: name 'signal' is not defined
```


```python
#Frequency and phase response
mfreqz(a)
```

```
---------------------------------------------------------------------------NameError
Traceback (most recent call last)<ipython-input-1-4b99431c27e4> in
<module>()
----> 1 mfreqz(a)
NameError: name 'mfreqz' is not defined
```


```python
#Impulse and step response
impz(a)
```

```
---------------------------------------------------------------------------NameError
Traceback (most recent call last)<ipython-input-1-e3d06ad960d4> in
<module>()
----> 1 impz(a)
NameError: name 'impz' is not defined
```



## Highpass FIR Filter

Let's define a highpass FIR filter, if you compare to original blog
post you'll notice that it has become easier since 2009. You don't
need to do ' spectral inversion "manually" anymore!



```python
n = 101
a = signal.firwin(n, cutoff = 0.3, window = "hanning", pass_zero=False)
mfreqz(a)
```

```
---------------------------------------------------------------------------NameError
Traceback (most recent call last)<ipython-input-1-ebfca740e426> in
<module>()
      1 n = 101
----> 2 a = signal.firwin(n, cutoff = 0.3, window = "hanning",
pass_zero=False)
      3 mfreqz(a)
NameError: name 'signal' is not defined
```



## Bandpass FIR filter

Notice that the plot has a caption defined in code chunk options.



```python
n = 1001
a = signal.firwin(n, cutoff = [0.2, 0.5], window = 'blackmanharris', pass_zero = False)
mfreqz(a)
```

```
---------------------------------------------------------------------------NameError
Traceback (most recent call last)<ipython-input-1-11bfdf306b97> in
<module>()
      1 n = 1001
----> 2 a = signal.firwin(n, cutoff = [0.2, 0.5], window =
'blackmanharris', pass_zero = False)
      3 mfreqz(a)
NameError: name 'signal' is not defined
```


