"from ... import module"   include module

The path is decided where are  you start

In the same folder
```
import module
module.fun1(a,b)
from module import fun1
fun1(a,b)
```

In the next  folder
```
from package import module
module.fun1(a,b)

from package.module import fun1
fun1(a,b)
```
change  the name of the function  or name of  the  module which is  you imported
```
import module as imp1
imp1.fun1(a,b)
from package.module  import fun1 as imp1
imp1(a,b)
```
inside the  folder  ,build a folder '\__init\__.py' , to tell program it is  a package

when the program run to 'import'  the module you imported  will run once
```
package1.__init__:
      from package1 import program1

outsideProgram.py:

      import package1      //' from . import program1'  has ran once

      package1.program1.show()
```
```
pythonLearn/
       outsideProgram.py
       outsideProgram2.py
       package1/
                __init__.py
               program1.py
               program2.py
               package2/
                       __init__.py
                       program1.py
               package3/
                       __init__.py
                       program1.py
```
Absolute  import
```
[yu@archlinux pythonLearn]$ python3 outsideProgram.py

Run in outsideProgram:

outsideProgram->outsideProgram2:
import outsideProgram2
outsideProgram2.show()

outsideProgram->package1.__init__:
import package1
package1.show()

outsideProgram->package1.program1:
from package1.package2 import program1
package1.package2.program1.show()


call in  package1.package2.program1:

package1.package2.program1->package1.package3.program1:
from package1.package3 import program1  as otherPG
otherPG.show()

package1.package2.program1->import outsideProgram2
import outsideProgram2
outsideProgram.show()
```
Relative import
```
call in  package1.package2.program1:

package1.package2.program1->package1.package3.program1:
from ..package3 import program1  as otherPG
otherPG.show()

package1.package2.program1->import outsideProgram2
from ... import outsideProgram2
ValueError: attempted relative import beyond top-level package

```
run program as a module
```
[yu@archlinux ~]$ python3 -m pythonLearn.outsideProgram

// now your program start from pythonLearn

package1.package2.program1->import outsideProgram2
from ... import outsideProgram2    //  from pythonLearn  import outsideProgram2
```
change path
```
//if program starts from package1.package2.program1
import sys
sys.path.insert(0, '..')
from package3 import program1 as pg3Show
pg3Show .show()
```
re-import
```
import importlib
importlib.reload(nameOfModule)
```
