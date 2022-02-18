# Getting ghostscript to work with Python (camelot, OS X)

When you try to use the camelot Python library, you might get the error message `Please make sure that Ghostscript is installed` even though ghostscript is installed! [The camelot docs give some tips](https://camelot-py.readthedocs.io/en/master/user/install-deps.html#for-ghostscript) but they didn't work for me.

## A solution that works

To solve this problem, we're going to create links from our ghostscript to the place camelot thinks ghostscript should be. We aren't moving it, we aren't copying it, we're just adding a little redirect that says "no, look for ghostscript in this other place!"

First, make sure you have ghostscript installed via homebrew.

```
brew install ghostscript
```

After you install ghostscript (or if it's already installed), run the following command to find out what version of ghostscript you have.

```
brew list --versions ghostscript
```

Mine is listed as **ghostscript 9.55.0**. To create the link between the `9.55.0` location and the location camelot wants ghostscript to be in, you'll run the command below. If you have a different version you'll need to edit the command.

```
sudo ln -s /opt/homebrew/Cellar/ghostscript/9.55.0/lib/libgs.dylib.9.55 /usr/local/lib/libgs.9.dylib
sudo ln -s /opt/homebrew/Cellar/ghostscript/9.55.0/lib/libgs.dylib.9.55 /usr/local/lib/libgs.dylib
sudo ln -s /opt/homebrew/Cellar/ghostscript/9.55.0/lib/libgs.dylib.9.55 /usr/local/lib/libgs.dylib.9.55
```

And then you're good to go! Go forth and use `.read_pdf` to read pdfs.

## Answers that do not work

When reading through [this GitHub thread](https://github.com/atlanhq/camelot/issues/184) I found a number of answers that didn't work!

First, this is how camelot tries to find ghostscript. Since it can't find it, it obviously doesn't work.

```
import ctypes
ctypes.util.find_library("gs")
```

Then some people said hey, use `distutils`, it can find ghostscript! Not for me, though.

```
import distutils.spawn

distutils.spawn.find_executable("gs")
```

And finally some people suggested adding library paths to your Jupyter notebook, which you do with `%env`. That also didn't work!

```
%env DYLD_LIBRARY_PATH="/opt/homebrew/lib"

import ctypes
ctypes.util.find_library("gs")
```